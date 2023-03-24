# 如何突出显示搜索结果

> 原文：<https://levelup.gitconnected.com/how-to-highlight-search-results-b64e3eb0f55c>

## 权威指南

在本教程中，你将学习如何在蝎狮搜索中突出搜索结果。如果您想提高应用程序或网站中搜索结果的可读性，可以从搜索结果突出显示中受益。

![](img/cbbda904d4f1a26e80a5e751e5f7bebf.png)

docs.manticoresearch.com 行动中的亮点

**高亮显示**允许您获取匹配关键词高亮显示的搜索结果片段。它有助于改善您的应用程序的搜索体验。

## 介绍

您可以使用多种方法在蝎狮搜索中高亮显示文本中的关键词。

*   语句[调用片段](https://docs.manticoresearch.com/latest/html/sphinxql_reference/call_snippets_syntax.html#call-snippets-syntax)允许从包含匹配的文档(称为片段)中获取片段列表。它可以与搜索查询分开使用，以突出显示一个字符串或字符串列表。这里有一个例子:

```
CALL SNIPPETS('my text with keyword', 'index', 'keyword');
```

*   函数 [SNIPPET()](https://docs.manticoresearch.com/latest/html/sphinxql_reference/call_snippets_syntax.html?#call-snippets-syntax) 使用指定的索引设置，从提供的数据和查询构建一个代码片段。该函数主要用于 SELECT 语句中，以突出显示给定的文本、字段值或使用 UDF(用户定义的函数)从另一个源获取的文本。它可以用来突出显示 match 子句中的同一个查询或另一个查询，这取决于您。像这样:

```
SELECT SNIPPET(content,'camera') FROM index WHERE MATCH('camera');
```

*   功能 [HIGHLIGHT()](https://docs.manticoresearch.com/latest/html/sphinxql_reference/select_syntax.html?&_ga=2.104354688.1651831658.1587113398-1034704496.1587113398#exist) 可用于突出显示搜索结果。此功能是在蝎狮 3.2.2 中添加的。将文档存储在蝎狮中时，可以更容易地突出显示文档中的关键词，而不仅仅是将它们编入索引。下面是一个调用示例:

```
SELECT HIGHLIGHT() FROM index WHERE MATCH('text feature');
```

前两个“CALL SNIPPETS”和“SNIPPET()”提供了获取包含与搜索关键字匹配的文档部分(称为 SNIPPET)列表的能力。最后一个“HIGHLIGHT()”从文档存储中获取所有可用的字段，并根据给定的查询突出显示它们。与 SNIPPET()不同，HIGHLIGHT()支持查询中的字段语法。

这三个都有相同的突出显示选项，我们将在接下来的步骤中讨论。在本教程中，我们将展示使用 HIGHLIGHT()的示例。

假设您有一个名为“highlight”的索引，其设置如下:

```
index highlight
{
        type = rt
        path = highlight
        rt_field = title
        rt_field = content
        rt_attr_uint = gid
        stored_fields = title, content
        index_sp = 1
        html_strip = 1
}
```

## 基本用法

一个简单的例子:

首先，添加一个文档:

```
INSERT INTO highlight(title,content,gid) VALUES('Syntax highlighting','Syntaxhighlighting is a feature of text editors that are used for programming, scripting, or markuplanguages, such as HTML. The feature displays text, especially source code, in different colors and fonts according to the category of terms.[1] This feature facilitates writing in a structured language such as a programming language or a markup language as both structures and syntax errors are visually distinct. Highlighting does not affect the meaning of the text itself;it is intended only for human readers.',1);
```

然后选择高亮显示():

```
SELECT HIGHLIGHT() AS h FROM highlight WHERE MATCH('text feature')\G
*************************** 1\. row ***************************
h: Syntax highlighting is a **<b>feature</b>** of **<b>text</b>** editors that are used ... , such as HTML. The**<b>feature</b>** displays **<b>text</b>**, especially source code, in ... of terms.[1] This **<b>feature</b>** facilitates writing in a structured ... affect the meaning of the **<b>text</b>** itself; it is intended ...
```

默认情况下，任何匹配的单词都用标记“粗体”突出显示，每个匹配项周围最多选择 5 个单词来组成一篇文章。
默认情况下，段落用“…”分隔。

HTML 标签用于突出显示匹配，因为片段通常显示在 HTML 内容中，但是您可以通过“匹配前”、“匹配后”、“环绕”和“块分隔”设置自定义行为。例如:

```
SELECT HIGHLIGHT({before_match='*',after_match='*',around=1,chunk_separator='###'}) AS h FROM highlight WHERE MATCH('text feature')\G
*************************** 1\. row ***************************
h: **###** a *****feature***** of *****text***###**. The *****feature***** displays *****text***###**] This *****feature***** facilitates**###** the *****text***** itself**###**
```

## 控制代码段的大小

默认设置将 256 个字符作为最大代码片段大小的限制(在同名设置“限制”下)。您可以像这样更改它:

```
SELECT HIGHLIGHT({limit=10}) AS h FROM highlight WHERE MATCH('text feature')\G
*************************** 1\. row ***************************
h:  ...  a **<b>feature</b>** ...
```

另一个可以更改的限制是由“limit_words”定义的片段中包含的字数:

```
SELECT HIGHLIGHT({limit_words=5},'content') AS h FROM highlight WHERE MATCH('text feature')\G
*************************** 1\. row ***************************
h: ... . The **<b>feature</b>** displays **<b>text</b>**, especially ...
```

也可以限制通道的数量，例如，如果我们只想获得一个通道:

```
SELECT HIGHLIGHT({limit_passages=1}) AS h FROM highlight WHERE MATCH('text feature')\G
*************************** 1\. row ***************************
h:  ...  languages, such as HTML. The **<b>feature</b>** displays **<b>text</b>**, especially source code, in ...
```

HIGHLIGHT()函数的默认行为是返回由限定空间内的分隔符分隔的段落。由于这一限制可能不足以涵盖所有段落，因此我们可能只获得部分段落。

为了演示它，让我们首先添加一个具有较长文本的文档。

```
INSERT INTO highlight(title,content) values('wikipedia','Syntax highlighting is a feature of text editors that are used for programming, scripting, or markup languages, such as HTML. The feature displays text, especially source code, in different colors and fonts according to the category of terms.[1] This feature facilitates writing in a structured language such as a programming language or a markup language as both structures and syntax errors are visually distinct. Highlighting does not affect the meaning of the text itself; it is intended only for human readers. Syntax highlighting is a form of secondary notation, since the highlights are not part of the text meaning, but serve to reinforce it. Some editors also integrate syntax highlighting with other features, such as spell-checking or code folding, as aids to editing which are external to the language. Contents 1Practical benefits 2Support in text editors 3Syntax elements 3.1Examples 4History and limitations 5See also 6References Practical benefits Highlighting the effect of missing delimiter (after watch=false) in Javascript Syntax highlighting is one strategy to improve the readability and context of the text; especially for code that spans several pages. The reader can easily ignore large sections of comments or code, depending on what they are looking for. Syntax highlighting also helps programmers find errors in their program. For example, most editors highlight string literals in a different color. Consequently, spotting a missing delimiter is much easier because of the contrasting color of the text. Brace MATCHing is another important feature of many popular editors. This makes it simple to see if a brace has been left out or locate the MATCH of the brace the cursor is on by highlighting the pair in a different color. A study published in the conference PPIG evaluated the effects of syntax highlighting on the comprehension of short programs, finding that the presence of syntax highlighting significantly reduces the time taken for a programmer to internalize the semantics of a program.[2] Additionally, data gathered FROM an eye-tracker during the study suggested that syntax highlighting enables programmers to pay less attention to standard syntactic components such as keywords. Support in text editors gedit supports syntax highlighting Some text editors can also export the colored markup in a format that is suitable for printing or for importing into word-processing and other kinds of text-formatting software; for instance asa HTML, colorized LaTeX, PostScript or RTF version of its syntax highlighting. There are several syntax highlighting libraries or "engines" that can be used in other applications, but are not complete programs in themselves, for example the Generic Syntax Highlighter (GeSHi) extension for PHP. For editors that support more than one language, the user can usually specify the language of the text, such as C, LaTeX, HTML, or the text editor can automatically recognize it based on the file extension or by scanning contents of the file. This automatic language detection presents potential problems. For example, a user may want to edit a document containing: morethan one language (for example when editing an HTML file that contains embedded Javascript code), a language that is not recognized (for example when editing source code for an obscure or relatively new programming language), a language that differs FROM the file type (for example when editing source code in an extension-less filein an editor that uses file extensions to detect the language). In these cases, it is not clear what language to use, and a document may not be highlighted or be highlighted incorrectly. Syntax elements Most editors with syntax highlighting allow different colors and text styles to be given to dozens of different lexical sub-elements of syntax. These include keywords, comments, control-flow statements, variables, and other elements. Programmers often heavily customize their settings in an attempt to show as much useful information as possible without making the code difficult to read. ');
```

现在让我们强调一下:

```
SELECT HIGHLIGHT({},'content') AS h FROM highlight WHERE MATCH('syntax')\G
*************************** 1\. row ***************************
h: **<b>syntax</b>** highlighting is a feature of ... language as both structures and **<b>syntax</b>** errors are visually distinct. Highlighting ... *************************** 2\. row *************************** h: ... version of its **<b>syntax</b>** highlighting. There are several **<b>syntax</b>** highlighting libraries ... highlighted incorrectly. **<b>syntax</b>** elements Most editors with **<b>syntax</b>** highlighting allow different ... different lexical sub-elements of **<b>syntax</b>**. These include keywords, comments, ...
```

对于新添加的文档，我们看到 HIGHLIGHT()并没有给出所有的段落。我们可以提高极限来克服它，问题是提高到什么程度。如果我们使用太高的值，HIGHLIGHT()将返回全部内容(包括高亮部分):

```
SELECT HIGHLIGHT({limit=10000},'content') AS h FROM highlight WHERE MATCH('syntax')\G
*************************** 1\. row ***************************
h: **<b>syntax</b>** highlighting is a feature of text editors that are used for programming, scripting, or markuplanguages, such as HTML. The feature displays text, especially source code, in different colors and fonts according to the category of terms.[1] This feature facilitates writing in a structured language such as a programming language or a markup language as both structures and **<b>syntax</b>** errors are visually distinct. Highlighting does not affect the meaning of the text itself; it is intended only for human readers. *************************** 2\. row *************************** h: **<b>syntax</b>** highlighting is a feature of text editors that are used for programming, scripting, or markuplanguages, such as HTML. The feature displays text, especially source code, in different colors and fonts according to the category of terms.[1] This feature facilitates writing in a structured language such as a programming language or a markup language as both structures and **<b>syntax</b>** errors are visually distinct. Highlighting does not affect the meaning of the text itself; it is intended only for human readers. **<b>syntax</b>** highlighting is a form of secondary notation, since the highlights are not part of the text meaning, but serve to reinforce it. Some editors also integrate **<b>syntax</b>** highlighting with other features, such as spell checking orcode folding, as aids to editing which are external to the language. Contents 1Practical benefits 2Support in text editors 3Syntax elements 3.1Examples 4History and limitations 5See also 6References Practical benefits Highlighting the effect of missing delimiter (after watch=false) in Javascript **<b>syntax</b>** highlighting is one strategy to improve the readability and context of the text; especially for code that spans several pages. The reader can easily ignore large sections of comments or code, depending on what they are looking for. **<b>syntax</b>** highlighting also helps programmers find errors in their program. For example, most editors highlight stringliterals in a different color. Consequently, spotting a missing delimiter is much easier because of the contrasting color of the text. Brace MATCHing is another important feature with many popular editors. This makes it simple to see if a brace has been left out or locate the MATCH of the brace the cursor is on by highlighting thepair in a different color. A study published in the conference PPIG evaluated the effects of **<b>syntax</b>** highlighting on the comprehension of short programs, finding that the presence of **<b>syntax</b>** highlighting significantly reduces the time taken for a programmer to internalise the semantics of a program.[2] Additionally, data gathered FROM an eye-tracker during the study suggested that **<b>syntax</b>** highlighting enables programmers to pay less attention to standard syntactic components such as keywords. Support in text editors gedit supports**<b>syntax</b>** highlighting Some text editors can also export the coloured markup in a format that is suitable for printing or for importing into word-processing and other kinds of text-formatting software; for instance asa HTML, colorized LaTeX, PostScript or RTF version of its **<b>syntax</b>** highlighting. There are several **<b>syntax</b>** highlighting libraries or "engines" that can be used in other applications, but are not complete programs in themselves, for example the Generic **<b>syntax</b>** Highlighter (GeSHi) extension for PHP. For editors thatsupport more than one language, the user can usually specify the language of the text, such as C, LaTeX, HTML,or the text editor can automatically recognize it based on the file extension or by scanning contents of the file. This automatic language detection presents potential problems. For example, a user may want to edit a document containing: more than one language (for example when editing an HTML file that contains embedded Javascript code), a language that is not recognized (for example when editing source code for an obscure or relatively new programming language), a language that differs FROM the file type (for example when editing source code in an extension-less file in an editor that uses file extensions to detect the language). In these cases, it is notclear what language to use, and a document may not be highlighted or be highlighted incorrectly. **<b>syntax</b>**elements Most editors with **<b>syntax</b>** highlighting allow different colors and text styles to be given to dozens of different lexical sub-elements of **<b>syntax</b>**. These include keywords, comments, control-flow statements, variables, and other elements. Programmers often heavily customize their settings in an attempt to show asmuch useful information as possible without making the code difficult to read.
```

如果我们只想突出显示段落而不是整个文本，我们需要使用选项“force_passages”:

```
SELECT HIGHLIGHT({limit=10000,force_passages=1},'content') AS h FROM highlight WHERE MATCH('syntax')\G
*************************** 1\. row ***************************
h: **<b>syntax</b>** highlighting is a feature of ... language as both structures and **<b>syntax</b>** errors are visually distinct. Highlighting ... *************************** 2\. row *************************** h: **<b>syntax</b>** highlighting is a feature of ... language as both structures and **<b>syntax</b>** errors are visually distinct. Highlighting ... intended only for human readers. **<b>syntax</b>** highlighting is a form of ... it. Some editors also integrate **<b>syntax</b>** highlighting with other features, such ... (after watch=false) in Javascript **<b>syntax</b>** highlighting is one strategy to ... what they are looking for. **<b>syntax</b>** highlighting also helps programmers find ... PPIG evaluated the effects of **<b>syntax</b>** highlighting on the comprehension of ... , finding that the presence of **<b>syntax</b>** highlighting significantly reduces the time ... during the study suggested that **<b>syntax</b>** highlighting enables programmers to pay ... in text editors gedit supports **<b>syntax</b>** highlighting Some text editors can ... version of its **<b>syntax</b>** highlighting. There are several **<b>syntax</b>** highlighting libraries or ... themselves, for example the Generic **<b>syntax</b>** Highlighter (GeSHi) extension for PHP ... be highlighted incorrectly. **<b>syntax</b>** elements Most editors with **<b>syntax</b>** highlighting allow different ... different lexical sub-elements of **<b>syntax</b>**. These include keywords, comments, control ...
```

另一种使整个文本突出显示的方法是简单地使用 limit=0:

```
SELECT HIGHLIGHT({limit=0},'content') AS h FROM highlight WHERE MATCH('text feature')\G
*************************** 1\. row ***************************
h: Syntax highlighting is a **<b>feature</b>** of **<b>text</b>** editors that are used for programming, scripting, ormarkup languages, such as HTML. The **<b>feature</b>** displays **<b>text</b>**, especially source code, in different colors and fonts according to the category of terms.[1] This **<b>feature</b>** facilitates writing in a structuredlanguage such as a programming language or a markup language as both structures and syntax errors are visuallydistinct. Highlighting does not affect the meaning of the **<b>text</b>** itself; it is intended only for human readers. *************************** 2\. row *************************** h: Syntax highlighting is a **<b>feature</b>** of **<b>text</b>** editors that are used for programming, scripting, ormarkup languages, such as HTML. The **<b>feature</b>** displays **<b>text</b>**, especially source code, in different colors and fonts according to the category of terms.[1] This **<b>feature</b>** facilitates writing in a structuredlanguage such as a programming language or a markup language as both structures and syntax errors are visuallydistinct. Highlighting does not affect the meaning of the **<b>text</b>** itself; it is intended only for human readers. Syntax highlighting is a form of secondary notation, since the highlights are not part of the **<b>text</b>** meaning, but serve to reinforce it. Some editors also integrate syntax highlighting with other features, suchas spell checking or code folding, as aids to editing which are external to the language. Contents 1Practical benefits 2Support in **<b>text</b>** editors 3Syntax elements 3.1Examples 4History and limitations 5See also 6References Practical benefits Highlighting the effect of missing delimiter (after watch=false) in Javascript Syntax highlighting is one strategy to improve the readability and context of the **<b>text</b>**; especially for code that spans several pages. The reader can easily ignore large sections of comments or code, depending on what they are looking for. Syntax highlighting also helps programmers find errors in their program. For example, most editors highlight string literals in a different color. Consequently, spotting a missing delimiter is much easier because of the contrasting color of the **<b>text</b>**. Brace MATCHing is another important **<b>feature</b>** with many popular editors. This makes it simple to see if a brace has been left out or locate the MATCH of the brace the cursor is on by highlighting the pair in a different color. A study published in the conference PPIG evaluated the effects of syntax highlighting on the comprehension of short programs, finding that the presence of syntax highlighting significantly reduces the time taken for a programmer to internalise the semantics of a program.[2] Additionally, data gathered FROM an eye-tracker during the study suggested that syntax highlighting enables programmers to pay less attention to standard syntactic components such as keywords. Support in **<b>text</b>** editors gedit supports syntax highlighting Some **<b>text</b>** editors can also export the coloured markup in a format that is suitable for printing or for importing into word-processing and other kinds of **<b>text</b>**-formatting software; for instance as a HTML, colorized LaTeX, PostScript or RTF version of its syntax highlighting. There are several syntax highlighting libraries or "engines" that can be used in other applications, but are not complete programs in themselves, for example the Generic Syntax Highlighter (GeSHi) extension for PHP. For editors that support more than one language, the user can usually specify the language of the **<b>text</b>**, such as C, LaTeX, HTML, or the **<b>text</b>** editor can automatically recognize it based on the file extension or by scanning contents of the file. This automatic language detection presents potential problems. For example, a user may want to edit a document containing: more than one language (for example when editing an HTML file that contains embedded Javascript code), a language that is not recognized (for example when editing source code for an obscure or relatively new programming language), a language that differs FROM the file type (for example when editing source code in an extension-less file in an editor that uses file extensions to detect the language). In these cases, it is not clear what language to use, and a document may not be highlighted or be highlighted incorrectly. Syntax elements Most editors with syntax highlighting allow different colors and **<b>text</b>** styles to be given to dozens of different lexical sub-elements of syntax. These include keywords, comments, control-flow statements, variables, and other elements. Programmers often heavily customize their settings in an attempt to show as much useful information as possible without making the code difficult to read.
```

## HTML 剥离和边界

如果我们的索引有句子检测，我们可以配置突出显示，不创建句子之间的段落:

```
SELECT HIGHLIGHT({},'content') AS h FROM highlight WHERE MATCH('html text')\G
*************************** 1\. row ***************************
h:  ...  highlighting is a feature of **<b>text</b>** editors that are used for ... markup languages, such as **<b>HTML</b>****.** The feature displays**<b>text</b>****,** especially source code ... affect the meaning of the**<b>text</b>**itself; it is intended only ... 1 row in set (0.00 sec)
```

在这个例子中，我们看到了“…标记语言，如 **HTML** 。该功能显示**文本**，尤其是跨句子的源代码……’。

用“passage_boundary=sentence”将这篇文章一分为二:

```
SELECT HIGHLIGHT({passage_boundary='sentence'},'content') AS h FROM highlight WHERE MATCH('html text')\G
*************************** 1\. row ***************************
h:  ...  highlighting is a feature of **<b>text</b>** editors that are used for ... , or markup languages, such as **<b>HTML</b>**. ... The feature displays **<b>text</b>**, especially source code, in different ... affect the meaning of the **<b>text</b>** itself; it is intended only ... 1 row in set (0.05 sec)
```

让我们添加一个包含 HTML 内容的文档。

```
INSERT INTO highlight(title,content) values('html content','The ideas of syntax highlighting overlap significantly with those of <a title="Structure editor" href="/wiki/Structure_editor">syntax-directed editors</a> One of the first such class of editors for code was Wilfred Hansens 1969 code editor, Emily.<sup id="cite_ref-hansen_3-0" class="reference"><a href="#cite_note-hansen-3">[3]</a></sup><sup id="cite_ref-4" class="reference"><a href="#cite_note-4">[4]</a></sup> It provided advanced language-independent <a title="Autocomplete" href="/wiki/Autocomplete">code completion</a> facilities, and unlike modern editors with syntax highlighting, actually made it impossible to create syntactically incorrect programs.');
```

默认情况下，高亮显示将根据索引设置处理 HTML 内容。如果在索引中启用了 HTML 剥离，那么 HIGHLIGHT()结果也将被剥离。

```
SELECT HIGHLIGHT({},'content') AS h FROM highlight WHERE MATCH('code class')\G
*************************** 1\. row ***************************
h:  ...  of the first such **<b>class</b>** of editors for **code** was Wilfred Hansens ... 1969 **<b>code</b>** editor, Emily.[3][4 ... ] It provided advanced language-independent **<b>code</b>** completion facilities, and unlike modern ...
```

如果我们希望突出显示也包含 HTML 标签，我们需要设置“html_strip_mode=none”:

```
SELECT HIGHLIGHT({html_strip_mode='none'},'content') AS h FROM highlight WHERE MATCH('code class')\G
*************************** 1\. row ***************************
h:  ... the first such **<b>class</b>** of editors for **<b>code</b>** was Wilfred ... 1969 **<b>code</b>** editor, Emily. <sup id="cite_ref-hansen_3-0" style="background: #EBE909; color: #000000;">**class**=" ... sup id="cite_ref-4" **<b>class</b>**="reference"><a title="Autocomplete" href="#cite_note- ... =">**<b>code</b>** completion facilities, and ... 1 row in set (0.05 sec)</a></sup>
```

请注意，“html_strip_mode=none”可以突出显示 html 语法中的单词，如“class”。
为了保护 HTML 实体，可以使用 retain 模式，但是对代码片段没有限制(limit=0):

```
SELECT HIGHLIGHT({html_strip_mode='retain',limit=0},'content') AS h FROM highlight WHERE MATCH('code class')\G *************************** 1\. row *************************** h:
<p>The ideas of syntax highlighting overlap significantly with those of <a title="Structure editor" href="/wiki/Structure_editor">syntax-directed editors</a> One of the first such **<b>class</b>** of editors for **<b>class</b>** was Wilfred Hansens 1969 **<b>code</b>** editor, Emily.<sup id="cite_ref-hansen_3-0" class="reference"><a href="#cite_note-hansen-3">[3]</a></sup><sup id="cite_ref-4" class="reference"><a href="#cite_note-4">[4]</a></sup> It provided advanced language-independent <a title="Autocomplete" href="/wiki/Autocomplete">**<b>code</b>** completion</a> facilities, and unlike modern editors with syntax highlighting, actually made it impossible to create syntactically incorrect programs</p>
```

本教程解释了如何在蝎狮搜索中高亮显示的几种方法。

![](img/29e3c58d8d28d0eaf800b116d342edb2.png)

蝎狮搜索—重点课程

# [互动课程](https://play.manticoresearch.com/highlighting/)

本教程以交互式课程的形式提供，其特点是有一个命令行，允许您交互式地使用上面的示例。