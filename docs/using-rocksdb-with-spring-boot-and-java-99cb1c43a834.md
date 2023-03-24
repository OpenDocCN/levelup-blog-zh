# 通过 Spring Boot 和 Java 使用 RocksDB

> 原文：<https://levelup.gitconnected.com/using-rocksdb-with-spring-boot-and-java-99cb1c43a834>

![](img/6eec74252cdedc310919e5975cd50e9d.png)

照片由[奥斯汀·尼尔](https://unsplash.com/@arstyy?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

[RocksDB](https://rocksdb.org/) 是脸书的嵌入式键值存储，是 Google 的 [LevelDB](https://github.com/google/leveldb) 的分支。它被用作多个数据库的存储层，例如[cocroach db](https://www.cockroachlabs.com/)。您可以将它用作嵌入式存储、缓存(而不是 Redis)、您自己的定制数据库、文件系统或存储解决方案的存储层等。

> *TL；博士:这里是* [*代号*](https://github.com/ukchukx/rocksdb-example) *。*

在 pom.xml 中，添加以下依赖项以引入 RocksDB:

```
<dependency>                           
    <groupId>org.rocksdb</groupId> 
    <artifactId>rocksdbjni</artifactId>  
    <version>6.6.4</version>   
</dependency>
```

如果你用格雷德…你就要自己照顾自己了，我很抱歉。\_(ツ)_/

作为参考，可以看看我自己的 [pom.xml](https://github.com/ukchukx/rocksdb-example/blob/master/pom.xml) 。

之后，我们想要描述一个存储库接口，通过它我们的应用程序可以与一般的存储服务，尤其是 RocksDB 进行交互。

```
import java.util.Optional;public interface KVRepository<K, V> {
  boolean save(K key, V value);
  Optional<V> find(K key);
  boolean delete(K key);
}
```

保存、查找和删除是我们对任何键值存储的基本要求，所以我们定义了这些。有了这个接口，我们可以使用任何键值存储，而无需改变应用程序的其他部分，这是一个很好的设计。
接下来，我们创建我们的 RocksDB 存储库作为这个接口的实现:

```
import lombok.extern.slf4j.Slf4j;
import org.rocksdb.Options;
import org.rocksdb.RocksDB;
import org.rocksdb.RocksDBException;
import org.springframework.stereotype.Repository;
import org.springframework.util.SerializationUtils;import javax.annotation.PostConstruct;
import java.io.File;
import java.io.IOException;
import java.nio.file.Files;
import java.util.Optional;@Slf4j
@Repository
public class RocksDBRepository implements KVRepository<String, Object> {
  private final static String FILE_NAME = "spring-boot-db";
  File baseDir;
  RocksDB db; @PostConstruct // execute after the application starts.
  void initialize() {
    RocksDB.loadLibrary();
    final Options options = new Options();
    options.setCreateIfMissing(true);
    baseDir = new File("/tmp/rocks", FILE_NAME); try {
      Files.createDirectories(baseDir.getParentFile().toPath());
      Files.createDirectories(baseDir.getAbsoluteFile().toPath());
      db = RocksDB.open(options, baseDir.getAbsolutePath()); log.info("RocksDB initialized");
    } catch(IOException | RocksDBException e) {
      log.error("Error initializng RocksDB. Exception: '{}', message: '{}'", e.getCause(), e.getMessage(), e);
    }
  } @Override
  public synchronized boolean save(String key, Object value) {
    log.info("saving value '{}' with key '{}'", value, key); try {
      db.put(key.getBytes(), SerializationUtils.serialize(value));
    } catch (RocksDBException e) {
      log.error("Error saving entry. Cause: '{}', message: '{}'", e.getCause(), e.getMessage()); return false;
    } return true;
  } @Override
  public synchronized Optional<Object> find(String key) {
    Object value = null; try {
      byte[] bytes = db.get(key.getBytes());
      if (bytes != null) value = SerializationUtils.deserialize(bytes);
    } catch (RocksDBException e) {
      log.error(
        "Error retrieving the entry with key: {}, cause: {}, message: {}", 
        key, 
        e.getCause(), 
        e.getMessage()
      );
    } log.info("finding key '{}' returns '{}'", key, value); return value != null ? Optional.of(value) : Optional.empty();
  } @Override
  public synchronized boolean delete(String key) {
    log.info("deleting key '{}'", key); try {
      db.delete(key.getBytes());
    } catch (RocksDBException e) {
      log.error("Error deleting entry, cause: '{}', message: '{}'", e.getCause(), e.getMessage()); return false;
    } return true;
  }
}
```

当应用程序在`initialize()`启动时，我们初始化我们的数据库。RocksDB 是一个低级存储，所以在与它在`save()`、`find()`和`delete()`中交互之前，我们需要将我们的键值对序列化为字节。

这就是全部…真的。

在这个阶段，我们的存储库已经完成，但还不太可用。让我们添加一个控制器，使我们能够与存储库进行交互:

```
import com.ukchukx.rocksdbexample.repository.KVRepository;
import lombok.extern.slf4j.Slf4j;
import org.springframework.http.MediaType;
import org.springframework.http.ResponseEntity;
import org.springframework.http.HttpStatus;
import org.springframework.web.bind.annotation.DeleteMapping;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;import java.util.Optional;@Slf4j
@RestController
@RequestMapping("/api")
public class Api {
  private final KVRepository<String, Object> repository; public Api(KVRepository<String, Object> repository) {
    this.repository = repository;
  } // curl -iv -X POST -H "Content-Type: application/json" -d '{"bar":"baz"}' http://localhost:8080/api/foo
  @PostMapping(value = "/{key}", 
              consumes = MediaType.APPLICATION_JSON_VALUE, 
              produces = MediaType.APPLICATION_JSON_VALUE)
  public ResponseEntity<Object> save(@PathVariable("key") String key, @RequestBody Object value) {
    return repository.save(key, value) 
      ? ResponseEntity.ok(value) 
      : ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).build();
  } // curl -iv -X GET -H "Content-Type: application/json" http://localhost:8080/api/foo
  @GetMapping(value = "/{key}", produces = MediaType.APPLICATION_JSON_VALUE)
  public ResponseEntity<Object> find(@PathVariable("key") String key) {
    return ResponseEntity.of(repository.find(key));
  } // curl -iv -X DELETE -H "Content-Type: application/json" http://localhost:8080/api/foo
  @DeleteMapping(value = "/{key}", produces = MediaType.APPLICATION_JSON_VALUE)
  public ResponseEntity<Object> delete(@PathVariable("key") String key) {
    return repository.delete(key) 
      ? ResponseEntity.noContent().build() 
      : ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).build();
  }
}
```

我们声明了一个简单的 REST 控制器，它公开了保存、查找和删除端点。这就是全部了。

继续使用 cURL 或 Postman 保存、查找和删除项目。