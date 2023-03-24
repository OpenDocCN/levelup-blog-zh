# 测试带服务的弯管

> 原文：<https://levelup.gitconnected.com/test-angular-pipes-with-services-4cf77e34e576>

## 如何测试使用注入服务的角形管道

![](img/23852baacbe930c8a607bf976c33003c.png)

照片由 [Guillaume TECHER](https://unsplash.com/@guillaume_t?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/free?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

我每天分享[一个小技巧](https://medium.com/@david.dalbusco/one-trick-a-day-d-34-469a0336a07e)直到 2020 年 4 月 19 日新冠肺炎隔离期结束。离希望中的好日子还有 25 天。

我花了很多时间专注于编写新的 [Angular](https://angular.io) 组件及其相关的单元测试，以至于我甚至错过了今天早上的在线“单口相声”,几乎感觉我一天都在某种漩涡中度过。

反正我喜欢这个挑战。我不想跳过今天的博客帖子，我想和你分享我是如何测试我创建的一个新管道的。此外，我不假装是这项练习的冠军，因此，如果你注意到任何可以改进的地方，请给我留言，我很乐意提高我的技能🙏。

# 创建管道

让我们首先用`ng`命令行创建一个名为“filter”的空白管道。

```
ng g pipe filter
```

这将创建如下所示的空白管道:

```
import { Pipe, PipeTransform } from '@angular/core';

@Pipe({
  name: 'filter'
})
export class FilterPipe implements PipeTransform {

  transform(value: any, ...args: any[]): any {
    return null;
  }

}
```

和它相关的测试:

```
import { FilterPipe } from './filter.pipe';

describe('FilterPipe', () => {
  it('create an instance', () => {
    const pipe = new FilterPipe();
    expect(pipe).toBeTruthy();
  });
});
```

你可以是也可以不是一个狂热爱好者，但是我想我们都同意，拥有一个无需任何努力就能创建类和相关测试的 CLI 是非常酷的。

# 创建服务

正如 staten 在我的开场白中所说，我们的目标是测试一个使用注入服务的管道。

```
ng g service translation
```

出于演示目的，我们创建了这个虚拟服务“translation ”,除了“Génial”或“Awesome”作为可观察值之外，它返回的内容并不多。

```
import { Injectable } from '@angular/core';

import { Observable, of } from 'rxjs';

@Injectable({
    providedIn: 'root'
})
export class TranslationService {
    translate(lang: string): Observable<string> {
        return of(lang === 'fr' ? 'Génial' : 'Awesome');
    }
}
```

# 机具管道

我们的服务准备好了，我们用它来增强我们的管道。

```
import { Pipe, PipeTransform } from '@angular/core';

import { TranslationService } from './translation.service';
import { Observable } from 'rxjs';

@Pipe({
    name: 'filter'
})
export class FilterPipe implements PipeTransform {
    constructor(private translationService: TranslationService) {}

    transform(lang: string): Observable<string> {
        return this.translationService.translate(lang);
    }
}
```

顺便说一下，它可以在模板中的`async`管道的帮助下使用(在下面的例子中，`lang`是组件的公共字符串变量)

```
<textarea [value]="lang | filter | async"></textarea>
```

# 更新管道测试

在本地，我仍然能够运行我的测试而没有错误，但是，因为我们现在在我们的管道中注入了一个服务，如果我们打开相关的单元测试，我们会注意到在构造函数`TS2554: expected 1 arguments, but got 0`上有一个 TypeScript 错误。为了解决这个问题，我们现在要么注入服务，要么模仿它。

## 测试中的解析服务

您可以通过`inject`功能或`TestBed`解决服务问题。因为第一个解决方案对我没有用，所以第二个是我的退路。

```
import { FilterPipe } from './filter.pipe';
import { TestBed } from '@angular/core/testing';
import { TranslationService } from './translation.service';

describe('FilterPipe', () => {
  beforeEach(() => {
    TestBed
      .configureTestingModule({
        providers: [
          TranslationService
        ]
      });
  });

  it('create an instance', () => {
    const service: TranslationService =
                              TestBed.get(TranslationService);

    const pipe = new FilterPipe(service);
    expect(pipe).toBeTruthy();
  });
});
```

## 模拟服务

另一个解决方案，也是我最终应用的，是创建一个服务的模拟，而不是提供它。

```
import { FilterPipe } from './filter.pipe';
import { of } from 'rxjs';
import { TranslationService } from './translation.service';

describe('FilterPipe', () => {
  let translationServiceMock: TranslationService;

  beforeEach(() => {
    translationServiceMock = {
      translate: jest.fn((lang: string) => of('Awesome'))
    } as any;
  });

  it('create an instance', () => {
    const pipe = new FilterPipe(translationServiceMock);
    expect(pipe).toBeTruthy();
  });
});
```

# 测试管道转换

到目前为止，我们能够测试我们的管道可以被创建，即使它依赖于一个服务，但是我们仍然没有有效地测试它的结果。因此，这是最后一部分，我使用了服务的模拟。基本上，一旦创建了管道，我们就可以访问它的`transform`方法，并继续进行一些常见的测试。

```
import { FilterPipe } from './filter.pipe';
import { of } from 'rxjs';
import { take } from 'rxjs/operators';
import { TranslationService } from './translation.service';

describe('FilterPipe', () => {
  let translationServiceMock: TranslationService;

  beforeEach(() => {
    translationServiceMock = {
      translate: jest.fn((lang: string) => of('Awesome'))
    } as any;
  });

  it('create an instance', () => {
    const pipe = new FilterPipe(translationServiceMock);
    expect(pipe).toBeTruthy();
  });

  it('should translate', () => {
    const pipe = new FilterPipe(translationServiceMock);
    pipe.transform('en')
      .pipe(take(1))
      .subscribe((text: string) => {
        expect(text).not.toBe(null);
        expect(text).toEqual('Awesome');
      });
  });
});
```

# 摘要

我仍然需要一点时间来为项目找到正确的测试设置，特别是当它们是新的项目时，但是一旦一切就绪，并且一旦我可以访问`nativeElement`来执行查询，就像我在 Web 组件中所做的那样，我会感觉更舒服，并且开始变得有趣😁。

呆在家里，注意安全！

大卫