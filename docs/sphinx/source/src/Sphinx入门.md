# Sphinx入门
## 启动模板
```shell
$ sphinx-quickstart # y * *
```
## 定义文档结构
在文件`index.rst`中定义目录页
```rst
.. toctree::
   :maxdepth: 2

   usage/installation  
   usage/quickstart
   ...
```
下面显而易见就是每一部分的路径, 但是不包含文件拓展名.
### `.. toctree`指令
`.. toctree`是`Sphinx`定义的生成文档目录的指令

> 1. 最大深度: `:maxdepth: 2`
>
> 2. 章节编号: `:numbered:` 也可以限制深度
>
> 3. toctree名: `:caption: Table of Contents`
>
> 4. toctree链接: `:name: mastertoc`
>
> 5. 只显示一级标题: `:titlesonly:`
>
> 6. `glob`匹配顺序: `:glob:`
>
> 7. `glob`匹配逆序: `:reversed:`
>
>    > ```rst
>    > .. toctree::
>    >    :glob:
>    >    :reversed:
>    >    
>    >    intro*
>    >    recipe/*
>    >    *
>    > ```
>
> 8. 不插入链接: `:hidden:`
>
> 9. 只拥有一个顶级: `:includehidden:`

### 跨文件引用`:ref:`

在Sphinx源文件中，您可以使用标准的大多数功能 [reStructuredText](https://www.sphinx.org.cn/glossary.html#term-restructuredtext) 。 Sphinx还增加了一些功能。 例如，您可以使用 [`ref`](https://www.sphinx.org.cn/usage/restructuredtext/roles.html#role-ref) 角色以可移植的方式(适用于所有输出类型)添加跨文件引用。

例如，如果您正在查看HTML版本，则可以查看此文档的源代码 - 使用侧栏中的”显示源”链接。

## 记录对象

[待更新](https://www.sphinx.org.cn/usage/quickstart.html#)

## 基本配置`conf.py`

### 项目信息

1. **project**: 项目名称
2. **author**: 项目作者
3. **copyright**: `2008, Author Name` 风格的版权声明
4. **version**: 主要项目版本, 用作 `|version|` 的替代品
5. **release**: 完整的项目版本, 用作 `|release|` 的替代品

### 一般配置项

1. **extensions**: 模块名称为 [extensions](https://www.sphinx.org.cn/usage/extensions/index.html) 的字符串列表

2. **source_suffix**: 源文件的文件扩展名

   ```python
   source_suffix = {
       '.rst': 'restructuredtext',
       '.txt': 'restructuredtext',
       '.md': 'markdown',
   }
   ```

3. **source_encoding**: 所有reST源文件的编码, 推荐的编码和默认值是 `'utf-8-sig'` 

4. **source_parsers**

   ```python
   source_parsers = {'.md': 'recommonmark.parser.CommonMarkParser'}
   ```

   使用markdown时， 必须安装解析器`recommonmark`

   ```shell
   pip install --upgrade recommonmark
   ```

   之后需要更新多个`conf.py`变量

   ```python
   extensions = ['recommonmark']
   source_suffix = {
       '.rst': 'restructuredtext',
       '.txt': 'markdown',
       '.md': 'markdown',
   }
   ```

   可以进一步配置[*recommonmark*](https://recommonmark.readthedocs.io/en/latest/auto_structify.html) 以允许标准 *CommonMark* 不支持的自定义语法


5. **master_doc**: The document name of the “master” document, default is index

6. **exclude_patterns**: 排除的`glob`模式

7. **language**: `zh_CN` 简体中文

8. [其他精细配置](https://www.sphinx.org.cn/usage/configuration.html#project-information)

### 主题配置`html_theme`

[可以创建自定义主题](https://www.sphinx.org.cn/theming.html)

但是一般用现有的就可以了:)

除了变量`html_theme`外有时还需要`html_theme_options`配套使用

1. 内置主题: 只需要改`html_theme`

2. pip主题: 只需要改`html_theme`

3. 主题包: 还需设置`html_theme_path = ["."]`


<table class="longtable docutils align-default">
   <colgroup>
   <col style="width: 50%">
   <col style="width: 50%">
   </colgroup>
   <tbody>
   <tr class="row-odd"><td><p><strong>内置主题概述</strong></p></td>
   <td></td>
   </tr>
   <tr class="row-even"><td><p><img alt="alabaster" src="https://www.sphinx.org.cn/_images/alabaster.png"></p>
   <p><em>alabaster</em></p>
   </td>
   <td><p><img alt="classic" src="https://www.sphinx.org.cn/_images/classic.png"></p>
   <p><em>classic</em></p>
   </td>
   </tr>
   <tr class="row-odd"><td><p><img alt="sphinxdoc" src="https://www.sphinx.org.cn/_images/sphinxdoc.png"></p>
   <p><em>sphinxdoc</em></p>
   </td>
   <td><p><img alt="scrolls" src="https://www.sphinx.org.cn/_images/scrolls.png"></p>
   <p><em>scrolls</em></p>
   </td>
   </tr>
   <tr class="row-even"><td><p><img alt="agogo" src="https://www.sphinx.org.cn/_images/agogo.png"></p>
   <p><em>agogo</em></p>
   </td>
   <td><p><img alt="traditional" src="https://www.sphinx.org.cn/_images/traditional.png"></p>
   <p><em>traditional</em></p>
   </td>
   </tr>
   <tr class="row-odd"><td><p><img alt="nature" src="https://www.sphinx.org.cn/_images/nature.png"></p>
   <p><em>nature</em></p>
   </td>
   <td><p><img alt="haiku" src="https://www.sphinx.org.cn/_images/haiku.png"></p>
   <p><em>haiku</em></p>
   </td>
   </tr>
   <tr class="row-even"><td><p><img alt="pyramid" src="https://www.sphinx.org.cn/_images/pyramid.png"></p>
   <p><em>pyramid</em></p>
   </td>
   <td><p><img alt="bizstyle" src="https://www.sphinx.org.cn/_images/bizstyle.png"></p>
   <p><em>bizstyle</em></p>
   </td>
   </tr>
   </tbody>
   </table>

每一个主题都可以有进一步配置, 具体见[here](https://www.sphinx.org.cn/usage/theming.html)

有许多第三方主题可用. 其中一些是一般用途, 而另一些则是针对单个项目的. 下面列出了第三方主题的一部分. 在 [PyPI](https://pypi.org/search/?q=&o=&c=Framework+%3A%3A+Sphinx+%3A%3A+Theme), [GitHub](https://github.com/search?utf8=✓&q=sphinx+theme&type=) 和 [sphinx-themes.org](https://sphinx-themes.org/) 上可以找到更多.

第三方经典主题: *sphinx_rtd_theme*

![](https://www.sphinx.org.cn/_images/sphinx_rtd_theme.png)

```python
$ pip install sphinx-rtd-theme -i https://pypi.tuna.tsinghua.edu.cn/simple
```

### Autodoc设置

[待更新](https://www.sphinx.org.cn/usage/quickstart.html)

### Intersphinx设置

[待更新](https://www.sphinx.org.cn/usage/quickstart.html)

### 其他

[其他拓展](https://www.sphinx.org.cn/usage/extensions/index.html)

[Setuptools集成](https://www.sphinx.org.cn/usage/advanced/setuptools.html)

[模板](https://www.sphinx.org.cn/templating.html#templating)

[写扩展](https://www.sphinx.org.cn/extdev/index.html#dev-extensions)

## 构建

[`sphinx-build`](https://www.sphinx.org.cn/man/sphinx-build.html)命令或者直接使用make脚本

语法:

```shell
make html
```
## 多文件多类型构建
待更新...









