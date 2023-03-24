# 在一个有标点符号和/或空格的句子中查找回文

> 原文：<https://levelup.gitconnected.com/find-palindrome-in-a-sentence-with-punctuation-and-or-spaces-in-golang-d47974e47749>

![](img/770fb88a00230c4ad3aa11631ae32997.png)

# 问题

**回文**:前后读相同的字符串，例如:anna，racecar，bananab

**定义**:如果去掉空格和标点符号，得到的字符串就是一个回文

样本 01:“从来没有一英尺太远，甚至。”→真实
示例 02:“，，，即使是一英尺也不要太远。”→真
样本 03: ""hello world"" →假
样本 04: ""racecar"" →真

# 解决方案 01

```
package mainimport "fmt"
import "unicode"func main() {
    input := [4]string{
        "never a foot too far, even.",     // Input 1 --> true
        ",,,,never a foot too far, even.", // Input 2 --> true
        "\"hello world\"",                 // Input 3 --> false
        "\"racecar\"",                     // Input 5 --> true
    } for _, sentence := range input {
        isPalindrome := isPalindrome(sentence)
        fmt.Printf("%s is %t\n", sentence, isPalindrome)
    }
}func isPalindrome(sentence string) bool {
    left := 0
    right := len(sentence)-1

    for {
        if left > right {
            break
        } for {
            if unicode.IsLetter(rune(sentence[left])) == true {
                break
            }
            left += 1
        }
        for {
            if unicode.IsLetter(rune(sentence[right])) == true {
                break
            }
            right -= 1
        }

        if sentence[left] != sentence[right] {
            return false
        }
        left += 1
        right -= 1
    }
    return true
}
```

## 时间和空间复杂性

*   **时间复杂度**:O(N *(N-1)+(N-1))。给定 N 是字符串中的字符数。
*   **空间复杂度** : O(1)。没有使用辅助存储空间。

# 解决方案 02

```
package mainimport (
    "fmt"
    "regexp"
)func main() {
    input := [4]string{
        "never a foot too far, even.",     // Input 1 --> true
        ",,,,never a foot too far, even.", // Input 2 --> true
        "\"hello world\"",                 // Input 3 --> false
        "\"racecar\"",                     // Input 5 --> true
    } for _, sentence := range input {
        isPalindrome := isPalindrome(sentence)
        fmt.Printf("%s is %t\n", sentence, isPalindrome)
    }
}func isPalindrome(sentence string) bool {
    reg, err := regexp.Compile("[^a-zA-Z0-9]+")
    if err != nil {
        fmt.Printf("%v\n", err.Error())
    }
    processedSentence := reg.ReplaceAllString(sentence, "") left := 0
    right := len(processedSentence) - 1 for {
        if left > right {
            break
        } if processedSentence[left] != processedSentence[right] {
            return false
        }
        left += 1
        right -= 1
    }
    return true
}
```

## 时间和空间复杂性

*   **时间复杂度** : O(N+N)。给定 N 是字符串中的字符数。
*   **空间复杂度** : O(1)。没有使用辅助存储空间。

# 外卖食品

感谢您阅读这个简短的解题问题。如果有人知道更好或更快的时间复杂度来解决这个问题，请随意评论和反馈。和平！✌️