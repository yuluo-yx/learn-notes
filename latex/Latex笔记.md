# Latex笔记

*****

## 1，latex源文件的基本结构

```Latex
% 导言区
\documentclass{book/article} %book report, letter

% 文章标题
\title{latex基础练习文档}
% 作者
\author{yuluo}
% 日期
\date{\today}

%正文区 （文稿区）
%\begin{环境名称}
%	内容……
%\end{环境名称}

\begin{document}
	\maketitle
	
	hello world
	
	% 数学公式的表达
	%Let $f(x)$ be defined by the formula
	%$f(x)=3x^2+x-1$.
	
	Let $f(x)$ be defined by the formula
	$$f(x)=3x^2+x-1$$ which is a polynomial of degree 2.
	
\end{document}
```

使用begin和end标签引入一个环境，**注意：在一个latex文件中只能有一个正文环境**

%：表示在latex文本中的注释

可以在源文件中加入空行，以使生成的文档结构比较清晰

为了能在文章中正确的显示出文章的标题和作者等信息，需要在正文中加入\maketitle

可以通过\documentclass{****}引入文档类，指定文本的结构，比如book和article。letter等

在latex中输入数学公式：使用$$符号包围的是数学公式。在$\$之外的属于文本内容，在latex中单$符号表示行内公式，双$符号表示行间公式

## 2，latex中文处理方法

```latex
% 导言区
\documentclass{book/article} %book report, letter
% 还有ctexbook ctexart ctexrep 使用此文档类时不需要引用中文宏包

%引入中文宏包
\usepackage{ctex}
% 文章标题
\title{latex基础练习文档}
% 作者
\author{\kaiti yuluo}
% 日期
\date{\today}

%正文区 （文稿区）
%\begin{环境名称}
%	内容……
%\end{环境名称}

\begin{document}
	\maketitle
	
	hello world
	
	% 数学公式的表达
	%Let $f(x)$ be defined by the formula
	%$f(x)=3x^2+x-1$.
	
	Let $f(x)$ be defined by the formula
	$$f(x)=3x^2+x-1$$ which is a polynomial of degree 2.
	
	% equation环境
	\begin{equation}
	AB^2 = BC^2 + AC^2
	\end{equation}
	
\end{document}
```

**注意：将源文件编码格式设置为UTF-8，将编译格式设置为XeLatex**

在latex中使用\usepackage(ctex)来引用中文宏包，在文档中显示中文

同时也可以在文本前面加入\字体名 来指定字体格式

equation环境用来引入带有编号的数学公式

> 可以在cmd窗口中通过texdoc "文件名"的方式来查看相关的文档
>
> ```shell
> 查看ctex宏包文档
> texdoc ctex
> 查看latex帮助文档
> texdoc lshort-zh
> ```

## 3，Latex字体字号设置

在latex中一个字体有五种属性，分别为

- 字体编码

​			正文字体编码：OT1，T1，EU1

​			数学字体编码：OML，OMS，OMX等

- 字体族

​			罗马字体：笔画起始处有装饰

​			无衬线字体：笔记起始处无装饰

​			打字机字体：每个字符宽度不同，又称为等宽字体

- 字体系列

​			粗细

​			宽度

- 字体形状

​			直立

​			斜体

​			伪斜体

​			小型大写

- 字体大小

### 2.1  设置字体族

```latex
% 导言区 引入文档类
\documentclass{article} %book report, letter
% 引入中文的宏包
\usepackage{ctex}
% 文章标题
\title{latex基础练习文档}
% 作者
% 指定字体
\author{\kaishu 姬世文}
% 日期
\date{\today}

%正文区 （文稿区）
\begin{document}
	\maketitle

	hello world!

	Let $f(x)$ be defined by the formula
	$$f(x)=3x^2+x-1$$ which is a polynomial of degree 2.

	% equation环境
	\begin{equation}
	AB^2 = BC^2 + AC^2
	\end{equation}

	% 字体族的设置（罗马字体 无衬线字体 打字机字体）

	% 设置为罗马字体
	\textrm{Roman Family}
	% 设置为无衬线字体
	\textsf{Sans Serif Family}
	% 设置为等宽字体
	\texttt{Typewriter Family}
	测试罗马字体的声明	

	% 通过声明，声明后续的字体为罗马字体
	\rmfamily Roamn Family

	% 通过大括号的限定来声明作用字体的范围
	{\sffamily who you are? you find self on everyone around!}
	
	{\ttfamily who you are? you find self on everyone around!}
	
\end{document}
```

{}：设定当前字体族的有效范围

### 2.2 设置字体系列（粗细，宽度）

````Latex
% 导言区 引入文档类
\documentclass{article} %book report, letter
% 引入中文的宏包
\usepackage{ctex}
% 文章标题
\title{latex基础练习文档}
% 作者
% 指定字体
\author{\kaishu 姬世文}
% 日期
\date{\today}

%正文区 （文稿区）
\begin{document}
	\maketitle

	hello world!

	Let $f(x)$ be defined by the formula
	$$f(x)=3x^2+x-1$$ which is a polynomial of degree 2.

	% equation环境
	\begin{equation}
	AB^2 = BC^2 + AC^2
	\end{equation}

	% 字体族的设置（罗马字体 无衬线字体 打字机字体）

	% 设置为罗马字体
	\textrm{Roman Family}

	\textsf{Sans Serif Family}

	\texttt{Typewriter Family}

	% 通过声明，声明后续的字体为罗马字体
	\rmfamily Roamn Family

	% 通过大括号的限定来声明作用字体的范围
	{\sffamily who you are? you find self on everyone around!}
	
	{\ttfamily who you are? you find self on everyone around!}
	
	% 字体系列设置（粗细，宽度）
	\textmd{Medium Series} \textbf{Boldface Series}

	{\mdseries Medium Series} {\bfseries Boldface Series}

	% 字体形状设置（直立，斜体，伪斜体，小型大写）
	
	% 字体设置命令
	\textup{Upright Shape} \textit{Italic Shape}
	\textsl{Slanted Shape} \textsc{Small Caps Shape}

	% 字体设置声明
	{\upshape Upright Shape} {\itshape Italic Shape} 
	{\slshape Slanted Shape} {\scshape Small Caps Shape}

\end{document}
````

````Latex
	% 中文字体设置
	{\songti 宋体} \quad
	{\heiti 黑体} \quad
	{\fangsong 仿宋} \quad
	{\kaishu 楷书} \quad
````

````Latex
	% 字体大小设置
	{\tiny 				yuluo}\\
	{\scriptsize 		yuluo}\\
	{\footnotesize		yuluo}\\
	{\small				yuluo}\\
	{\normalsize   		yuluo}\\
	{\large 			yuluo}\\
	{\Large 			yuluo}\\
	{\huge 				yuluo}\\
	{\Huge				yuluo}\\
````

> 对于normalsize大小设置在\documentclass[]{article}中设置 数值大小只有10， 11， 12 三个值

**注意：latex中强调的是文本内容与格式分离**

在设置字体的时候可以使用\newcommand命令新建一个命令来完成对字体格式的设置

````Latex
%新建字体设置命令
\newcommand{\myfont}{\textit{\textbf{\textsf{Fancy Text}}}}

\myfont
````

## 4，文章的篇章结构

````Latex
% 导言区
\documentclass{article}

\usepackage{ctex}

% 正文区（文稿区）
\begin{document}
	% 构建小节
	\section{引言}
	这是一个latex篇章结构的引言

	这是第二自然段 \par 第三自然段
	\section{实验方法}
	\section{实验结果}
	% 利用subsection构建子小节
	\subsection{数据}
	\subsection{图表}
	\subsubsection{实验条件}
	\subsubsection{实验过程}
	\subsection{结果分析}
	\section{结论}
	\section{致谢}

\end{document}
````

在区分自然段时一个空格代表下一个自然段，多个空行和一个空行效果是相同的

\par：生成一个新的自然段

可以自定义设置大纲格式：

````latex
% 导言区
\documentclass{ctexart}

\usepackage{ctex}
%=========设置标题的格式=========
\ctexset{
	section = {
		format+ = \zihao{-4} \heiti \raggedright,
		name = {,、},
		number = \chinese{section},
		beforeskip = 1.0ex plus 0.2ex minus .2ex,
		afterskip = 1.0ex plus 0.2ex minus .2ex,
		aftername = \hspace{0pt}
		},
		subsection = {
			format+ = \zihao{-4} \heiti \raggedright,
			% name = {\thesubsection、},
			name = {,、},
			number = \arabic{subsection},
			beforeskip = 1.0ex plus 0.2ex minus .2ex,
			afterskip = 1.0ex plus 0.2ex minus .2ex,
			aftername = \hspace{0pt}
		}
}

% 正文区（文稿区）
\begin{document}
	% 构建小节
	\section{引言}
	这是一个latex篇章结构的引言

	这是第二自然段 \par 第三自然段
	\section{实验方法}
	\section{实验结果}
	% 利用subsection构建子小节
	\subsection{数据}
	\subsection{图表}
	\subsubsection{实验条件}
	\subsubsection{实验过程}
	\subsection{结果分析}
	\section{结论}
	\section{致谢}

\end{document}
````

也可以利用\chpter产生带章节的大纲：

````Latex
% 导言区
\documentclass{ctexbook}

\usepackage{ctex}
%=========设置标题的格式=========
\ctexset{
	section = {
		format+ = \zihao{-4} \heiti \raggedright,
		name = {,、},
		number = \chinese{section},
		beforeskip = 1.0ex plus 0.2ex minus .2ex,
		afterskip = 1.0ex plus 0.2ex minus .2ex,
		aftername = \hspace{0pt}
		},
		subsection = {
			format+ = \zihao{-4} \heiti \raggedright,
			% name = {\thesubsection、},
			name = {,、},
			number = \arabic{subsection},
			beforeskip = 1.0ex plus 0.2ex minus .2ex,
			afterskip = 1.0ex plus 0.2ex minus .2ex,
			aftername = \hspace{0pt}
		}
}

% 正文区（文稿区）
\begin{document}

	% 构建章节
	\chapter{绪论}
	% 构建小节
	\section{研究的目的和意义}
	\section{国内外研究现状}
	\subsection{国外研究现状}
	\subsection{国内研究现状}
	\section{研究内容}
	\section{研究方法与技术路线}
	\subsection{研究方法}
	\subsection{技术路线}
	\chapter{实验与结果分析}
	\section{引言}
	这是一个latex篇章结构的引言

	这是第二自然段 \par 第三自然段

	\subsection{数据}
	\subsection{图表}
	% 利用subsection构建子小节
	\subsubsection{实验条件}
	\subsubsection{实验过程}
	\subsection{结果分析}
	\section{结论}
	\section{致谢}
\end{document}
````

同时，也可以利用\tableofcontents产生整个文章的大纲

````Latexx
\tableofcontents
````

## 5，特殊符号的处理

```Latex
空白符号:
% 空行分段，多个空行等同一个
% 自动缩进，绝对不能使用空格代替
% 英文中多个空格处理为一个空格。中文中空格将被忽略
% 汉字与其他字符的间距会自动由XeLaTex处理
% 禁止使用中文全角空格
```

> % 1em（当前字体中M的宽度）
>
> ```latex
> a\quad b
> ```
>
> % 2em
>
> ```Latex
> a\qquad b
> ```
>
> % 约为1/6个em
>
> ````Latex
> a\,b a\thinspace b
> ````
>
> % 0.5个em
>
> ````Latex
> a\enspace b
> ````
>
> % 输出一个空格
>
> ````Latex
> a\ b
> ````
>
> % 硬空格（不能分割的空格）
>
> ```Latex
> a~b
> ```
>
> % 1pc=12pt=4.218mm (指定宽度的空格)
>
> ```Latex
> a \kern 1pc b
> a \kern -1em b
> a \hskip 1em b
> a \hspace{35pt}b
> ```
>
> % 占位宽度空白
>
> ```latex
> a\hphantom{xyz}b
> ```
>
> % 弹性长度空白
>
> ```Latex
> a\hfill b
> ```

```Latex
% 导言区
\documentclass{article}

\usepackage{ctex}

% 正文区（文稿区）
\begin{document}
	\section{latex中的特殊符号}
	\subsection{空白符号}
	% 1em（当前字体中M的宽度）
	1a\quad b

	% 2em
	2a\qquad b

	% 约为1/6个em
	3a\,b a\thinspace b

	% 0.5个em
	4a\enspace b

	% 输出一个空格
	5a\ b

	% 硬空格（不能分割的空格）
	6a~b
	
	% 指定空格长度
	7a \kern 1pc b

	8a \kern -1em b

	9a \hskip 1em b

	10a \hspace{35pt}b
	
	% 占位宽度空白
	11a\hphantom{xyz}b

	% 弹性长度空白
	12a\hfill b

	\subsection{LaTex控制符}
	
	\# \$ \% \{ \} \~{} \_{} \^{} \textbackslash \&	

	\subsection{排版符号}
	
	\S \P \dag \ddag \copyright \pounds

	\subsection{Tex标志符号}

	\Tex{} \LaTex{} \LaTeXe{}

	\subsection{引号}

	% 1 左边的`(撇号)
	` 
	% 单引号字符表示右单引号
	'	
	% 连续两个撇号表示左双引号
	``
	% 连续两个单引号表示右双引号
	''
	``你好''

	\subsection{连字符}

	% 短连字符
	-
	% 中连字符
	--
	% 长连字符
	---

	\subsection{非英文字符}

	\oe \OE \ae \AE \aa \AA \o \O \l \L \ss \SS !` ?`

	\subsection{重音符号（以o为例）}
	
	\`o \'o \^o \''o \~o \=o \.o \u{o} \v{o} \h{o} \r{o} \t{o} \b{o} \c{o} \d{o}

\end{document}
```

**注意：编译时使用XeLatex编译，设置编码为utf-8编码**

## 6，Latex的插图

```Latex
% 导言区
\documentclass{ctexart} %ctexbook ctexrep

\usepackage{ctex}

% 导言区：\usepackage{graphicx}
% 语  法：\includegraphics[< 选项 >]{< 文件名 >}
% 格  式：EPS， PDF, PNG, JPEG, BMP
\usepackage{graphicx}
\graphicspath{{figures/}} %图片在当前目录下的fighres目录

% 正文区（文稿区）
\begin{document}

	插图:
	
	\includegraphics{1.jpg}

\end{document}
```

在导入图片前导入graphicx宏包

使用\includegraphics[]{image}，用必选参数指定文件名，但是可以不需要其余参数

可以选择多个文件夹，注意在引入的时候用大括号括住进行分组

## 7，表格制作与排版

````Latex
% 导言区
\documentclass{ctexart}

\usepackage{ctex}

% 正文区（文稿区）
\begin{document}
	% 使用tabular环境生成表格
	% 有一个必选参数，指定表格的内容对齐格式
	% l 左对齐 c 居中 r 右对齐 用\\结尾
	%  可以使用\\hline产生横线
	% 可以使用两个\\hline和两个 || 产生双竖线 
	\begin{tabular}{|l|c|c|c|r|}
	\hline
		姓名 & 语文 & 数学 & 操作系统 & 数据结构 \\
	\hline
		yuluo & 100 & 100 & 100 & 100 \\
	\hline
		huakai & 100 & 100 & 100 & 100 \\
	\hline
	\end{tabular}

	% 可以用p参数指定列格式的宽度，内容超过宽度时自动换行
	\begin{tabular}{|l|c|c|c|p{1.5cm}|}
	\hline
		姓名 & 语文 & 数学 & 操作系统 & 数据结构 \\
	\hline
		yuluo & 100 & 100 & 100 & 100 \\
	\hline
		huakai & 100 & 100 & 100 & 100 \\
	\hline
	\end{tabular}

\end{document}
````

可以使用tabular环境生成表格，用 & 分割列

有一个必选参数，指定表格的内容对齐格式

l 左对齐 c 居中 r 右对齐 用 \\  \\ 结尾

在必选参数中加入 | 表示分割线

**注意：空格是不起作用的， 可以使用\\hline产生横线**

可以使用两个\\hline和两个 || 产生双竖线

## 8，Latex中的浮动体

````Latex
% 导言区
\documentclass{ctexart}

\usepackage{ctex}
\usepackage{graphicx}
\graphicspath{{figures/}}

% 正文区（文稿区）
\begin{document}

	% 图像浮动体环境，可将图片放入此环境中
	\begin{figure}[htbp]
		% 可以采用centering 让环境中的内容居中排版
		\centering
		\includegraphics[scale=0.3]{1.jpg}
		% \caption命令设置图片标题
		\caption{text}
	\end{figure}

	在LaTex中也可以这样使用表\ref{tab-score}所示的表格
	% 表格浮动体环境,可将表格放入此环境中
	\begin{table}
		\centering
		\caption{考试成绩单}\label{tab-score}
		\begin{tabular}{|l|c|c|c|r|}
			\hline
			姓名 & 语文 & 数学 & 操作系统 & 数据结构 \\
			\hline
			yuluo & 100 & 100 & 100 & 100 \\
			\hline
			huakai & 100 & 100 & 100 & 100 \\
			\hline
		\end{tabular}
	\end{table}

	% 使用tabular环境生成表格
	% 有一个必选参数，指定表格的内容对齐格式
	% l 左对齐 c 居中 r 右对齐 用\\结尾
	%  可以使用\\hline产生横线
	% 可以使用两个\\hline和两个 || 产生双竖线 
	\begin{tabular}{|l|c|c|c|r|}
	\hline
		姓名 & 语文 & 数学 & 操作系统 & 数据结构 \\
	\hline
		yuluo & 100 & 100 & 100 & 100 \\
	\hline
		huakai & 100 & 100 & 100 & 100 \\
	\hline
	\end{tabular}

	% 可以用p参数指定列格式的宽度，内容超过宽度时自动换行
	\begin{tabular}{|l|c|c|c|p{1.5cm}|}
	\hline
		姓名 & 语文 & 数学 & 操作系统 & 数据结构 \\
	\hline
		yuluo & 100 & 100 & 100 & 100 \\
	\hline
		huakai & 100 & 100 & 100 & 100 \\
	\hline
	\end{tabular}

\end{document}
````

浮动题：对表格和图片内容进行排版。

可以通过 \\caption{浮动题标题} 来设置标题

可以通过可选参数[htbp]来设置浮动体的位置

可以通过 \\label{标签名}  设置标签的方式在其他的地方引用这个表格或者图片，实现交叉引用，并且使用空行实现正确的分段

## 9，LaTex中数学公式的引用初步

```Latex
% 导言区
\documentclass{ctexart}

\usepackage{ctex}
\usepackage{amsmath}

% 正文区（文稿区）
\begin{document}

	\section{简介}
	LaTex将排版内容分为文本模式和数学模式。文本模式用于普通文本的排版，数学模式用于数学公式的排版
	\section{行内公式}
	\subsection{美元符号}
		交换律是$a+b=b+a$，如\(1+2=2+1=3\)
	\subsection{小括号}
		交换律是\(a+b=b+a\)，如\(1+2=2+1=3\)
	\subsection{math环境}
		\begin{math}
			a+b=b+a
		\end{math}
		,如
		\begin{math}
			1+2=2+1=3
		\end{math}
	\section{上下标}
	\subsection{上标}
		$3x^2 - x + 2 = 0$
		% 如果x的指数是两位数，要用大括号进行分组，确保正确的格式
		$3x^{20} - x + 2 = 0$
		% 在LaTex中也可以将一个已有的公式进行上标处理，要用大括号
		$3x^{3x^2 - x + 2} - x + 2 = 0$
	\subsection{下标}
		a_0, a_1, a_2
		% 同样有多个数字时，要使用大括号
		a_{10}, a_{100}, a_{3x^2 - x + 2}
	\section{希腊字母}
		$\alpha$
		$\beta$
		$\gamma$
		$\epsilon$
		$\pi$
		$\omega$

		$\Gamma$
		$Delete$
		$\Theta$
		$\Pi$
		$\Omega$

		% 也可以用在数学公式的排版中
		$\alpha^3 + \beta^2 + \gamma = 0$

	\section{数学函数}
		$\log$
		$\sin$
		$\cos$
		$\arcsin$
		$\arccon$
		$\ln$

		$\sin^2 x + \cos^2 x = 1$

		y = \arcsin x$
	
		$y = \ln x$

		$y = \long_2 x$

		$\sqrt{2}$
		$\sqrt{x^2 +y^2}$
		$\sqrt{x + \sqrt{2}}$
		% 4次根号下x
		$\sqrt[4]{x}$

	\section{分式}

	大约是原体积的$3/4$
	% 前一个是分子，后一个是分母
	大约是原体积的$\frac{3}{4}$
	
	\section{行间公式}
	\subsection{美元符号}

	$$a+b=b+a$$
	如
	$$1+2=2+1=3$$

	\subsection{中括号}

	交换律是\[a+b=b+a\]
	如
	\[1+2=2+1=3\]

	\subsection{displaymath环境}

	\begin{displaymath}
	a+b=b+a
	\end{displaymath}
	如
	\begin{displaymath}
	1+2=2+1=3
	\end{displaymath}

	\subsection{自动编号公式equation环境}
	交换律见公式\ref{eq:commutative}
	% 为使公式在文本中自动编号需要使用equation环境
	\begin{equation}
	% 也可以对equation中的公式用\label命令添加标签
	a+b=b+a \label{eq:commutative}
	\end{equation}

	% 注意：带*的equation环境需要使用amsmath宏包
	\subsection{不编号公式equation*环境}

	交换律见公式\ref{eq:commutative2}

	% 如果不需要对公式进行编号，可以使用带*号的equation环境
	% 没有编号的公式在交叉引用的时候使用的是小节号
	\begin{equation*}
	a+b=b+a \label{eq:commutative2}
	\end{equation*}

\end{document}

```

## 10，在LaTex中排版矩阵的问题

````LaTex
% 导言区
\documentclass{ctexart}

\usepackage{ctex}
\usepackage{amsmath}

% 定义新命令
%\newcommand{\adots}{\mathinner}{\mkern2mu%
%					\raisebox{0.1em}{.}\mkern2mu\raisebox{0.4em{.}%
%					\mkern2mu\raisebox{0.7em{.}\mkern1mu}
%					}

% 正文区（文稿区）
\begin{document}

	% 在LaTex中可以使用matrix环境实现矩阵的排版,需要引入amsmath宏包,与表格环境类似，也可以使用&分割列，\\ 分割行
	\[
		\begin{matrix}
			0 & 1 \\
			1 & 0
		\end{matrix}
	\]

	% pmatrix 用于在矩阵两边加小括号
		\[
		\begin{pmatrix}
			0 & 1 \\
			1 & 0
		\end{pmatrix}
	\]

	% bmatrix 用于在矩阵两边加中括号
		\[
		\begin{bmatrix}
			0 & 1 \\
			1 & 0
		\end{bmatrix}
	\]

	% Bmatrix 用于在矩阵两边加大括号
		\[
		\begin{Bmatrix}
			0 & 1 \\
			1 & 0
		\end{Bmatrix}
	\]	

	% vatrix 用于在矩阵两边加单竖线
		\[
		\begin{vmatrix}
			0 & 1 \\
			1 & 0
		\end{vmatrix}
	\]

	% Vatrix 用于在矩阵两边加双竖线
		\[
		\begin{Vmatrix}
			0 & 1 \\
			1 & 0
		\end{Vmatrix}
	\]

	% 使用上下标
			\[
		\begin{pmatrix}
			a_{11}^2 & a_{12}^2 & a_{13}^2 \\
			0 & a_{22} & a_{23}\\
			0 & 0 & a_{33}
		\end{pmatrix}
	\]
	
	% 矩阵中常用的省略号可以使用\dots, \vdots, \ddots
	% 在数学模式中可以使用\time命令排版乘号
	% 使用\adots实现从右到左的点号，需要自定义一个新命令
	\[
		\begin{bmatrix}
			a_{11} & \dots & a_{1n}\\
%			\adots & \ddots & \vdots \\
			& \ddots & \vdots \\
			0 & & a_{nn}
		\end{bmatrix}_{n \times n}
	\]

	% 分块矩阵，嵌套矩阵
	% \text命令用于在数学模式中临时切换到文本模式
	\[
		\begin{pmatrix}
			\begin{matrix} 1 & 0 \\ 0 & 1 \end{matrix}
			& \text{\Large 0} \\
			\text{\Large 0} & \begin{matrix}
			1 & 0 \\ 0 & -1 \end{matrix}
		\end{pmatrix}
	\]

	% 三角矩阵
	% 可以使用\multicolumn合并多列 \raisebox合并多列
	\[\begin{pmatrix}
	a_{11} & a_{12} & \cdots & a_{1n} \\
	& a_{22} & \cdots & a_{2n} \\
	&        & \ddots & \vdots \\
	\multicolumn{2}{c}{\raisebox{1.3ex}[0pt]{\Huge 0}}
	&        & a_{nn}
	\end{pmatrix}
	\]

	% 跨列的省略号：\hdotsfor{<列数>}
	\[
	\begin{pmatrix}	
	1 & \frac 12 & \dots & \frac 1n \\
	\hdotsfor{4} \\
	m & \frac m2 & \dots & \frac mn
	\end{pmatrix}
	\]
	
	% 行内小矩阵 （smallmatrix）环境
	复数 & z = (x,y)& 也可以用矩阵
	\begin{math}
	\left(   % 需要手动加上左括号
	\begin{smallmatrix}
	x & -y \\ y & x
	\end{smallmatrix}
	\right) % 需要手动加上右括号
	\end{math} 来表示

	% array环境（类似于表格环境tabular）
	% 如果需要多个字母构成分母，需要{}分组
	\[
		\begin{array}{r|r}
		\frac12 & 0 \\
		\hline
		0 & -\frac abc \\
		\end{array}
	\]
	
	% 用array环境构造复杂环境
	\[
	% @{<内容>} -添加任意内容，不占表项计数
	% 此处添加一个负值空白，表示向左移-5pt的距离
	\begin{array}{c@{\hspace{-5pt}}l]
	% 第一行，第一列
	\left(
	\begin{array}{ccc|ccc}
	a & \cdots & a & b & \cdots & b \\
	& \ddots & \vdots & \vdots & \adots \\
	&				& a & b\\ \hline
	&				&   & c & \cdots & c\\
	&				&   & \vdots & & \vdots \\
	\multicolumn{3}{c|}{\raisebox}{2ex}[0pt]{\Huge 0}}
	& c & \cdots & c
	\end{array}
	\right
	& 
	% 第一行第二列
	\begin{array}{1}
	% \left.仅表示与\right\ 配对，什么都不输出
	\left.\rule{0mm}{7mm}\right\}p \\
	\\
	\left.\rule{0mm}{7mm}\right\}q
	\end{array}
	\\[-5pt]
	% 第二行第一列
	% \underbrace命令 排版横向大括号
	\begin{array}{cc}
	\underbrace{\rule{17mm}{0mm}}_m &
	\underbrace{\rule{17mm}{0mm}}_m 
	\end{array}
	% 第二行第二列
	\end{array}
	\]

\end{document}
````

## 11，LaTex中的多行数学公式

```Latex
% 导言区
\documentclass{ctexart}

% \usepackage{ctex}
\usepackage{amsmath}
\usepackage{amssymb}

% 正文区
\begin{document}
	
	% gather 和 gather*环境，可以使用\\换行
	% 带编号
	\begin{gather}
		a + b = b + a \\
		ab ba
	\end{gather}
	% 不带编号
	\begin{gather*}
		a + b = b + a
		ab ba
	\end{gather*}

	% 在\\前使用\notag阻止编号
	\begin{gather}
		a + b = b + a \notag \\ 
		ab ba \notag \\
		ac ca
	\end{gather}
	
	% 使用align和align*环境，（用&进行对齐）
	% 带编号
	\begin{align}
	x &= t + \cos t + 1 \\
	y &= 2\sin t
	\end{align}
	% 不带编号
	\begin{align*}
	x &= t & x &= \cos t & x &= t \\
	y &= 2t & y &= \sin(t+1) & y &= \sin t
	\end{align*}

	% 可以使用split环境（对齐采用align环境的方式。编号在中间）
	\begin{equation}
	\begin{split}
	\cos 2x &= \cos^2 x - \sin^2 x \\
	&= 2\cos^2 x - 1
	\end{split}
	\end{equation}
	
	% 分段函数的表示 使用cases环境
	% 每行公式中使用 & 分割为两部分，
	% 通常表示值和后面的条件
	% \mathbb用于输出花体字符
	% \in 表示属于符号
	% \text用于在数学模式中临时切换到文本模式
	\begin{equation}
	D(x) = \begin{cases}
	1, & \text{如果} x \in \mathbb{Q}；\\
	0, & \text{如果} x \in \mathbb{R}\setminus\mathbb{Q}；\\
	\end{cases}
	\end{equation}

\end{document}
```

## 12，LaTex中排版参考文献

````Latex
% 导言区
\documentclass{ctexart}
\usepackage{ctex}

% 正文区
\begin{document}

% 一次管理每一次使用
% 参考文献格式：
% \begin{thebibliography}{编号样本}
% 		\bibitem[记号]{引用标志}文献条目1
% 		\bibitem[记号]{引用标志}文献条目2
% 		\bibitem[记号]{引用标志}文献条目3
%		……
% \end{thebibliography}
% 	其中文献条目包括：作者，题目，出版社，年代，版本，页码等
% 引用时候要可以采用：\cite{引用标志1，引用标志2}

\begin{thebibliography}{99}
	\bibitem{atricle1}陈立辉，苏伟，蔡川，陈小云.\emph{基于LaTex的Web数学公式提取方法研究}[J].计算机科学.2014(06)
	\bibitem{book}William H. Press,Saul A. Teukolsky,
	William T. Vetterling, Brian P. Flannery,
	\emph{Numberical Recipes 3rd Edition:
	The Art of Scientific Computing}
	Cambridge University Press, New York,2007.
	\bibitem{latexGuide} Kopka Helmut, W. Daly Patrick,
	\emph{Guide to \LaTex}, $4^{th}$ Edition.
	Available at \texttt{http://www.amazong.com}.
	\bibitem{latexMath} Graetzer George, \emph{Math Into \LaTex},
	BirkhAuser Boston; 3 edition ()June 22, 2000).
\end{thebibliography}

% 需要在tex stdio文件进行相应设置
% biblatex/biber
% 新的TEX惨开文献排版引擎
% 样式文件（参考文献样式文件 --bbx文件，引用样式文件--cbx文件）使用LATEX编写
% 支持根据本地化排版，如：
%		biber -l zh_pinyin texfile, 用于指定按拼音排序
% 		biber -l zh_stroke texfile, 用于按笔画排序

\end{document}
````

## 13，LaTex中定义新的命令和环境

````Latex
% 导言区
\documentclass{ctexart}

% \newcommand-定义命令
% 命令只能由字母组成，不能以\end开头
% \newcommand<命令>[<参数个数>][<首参数默认值>]{<具体定义>}

% \newcommand可以是简单的字符串替换，例如
% 使用\PRC 相当于People`s Republic of \emph{China}这个内容
\newcommand\PRC{People`s Republic of \emph{China}}

% \newcommand也可以使用参数
% 参数个数可以从1到9，使用时用#1，#2……#9表示
\newcommand\loves[2]{#1 喜欢 #2}
\newcommand\hatedby[2]{#2 不受 #1 喜欢}

% \newcommand的参数也可以有默认值
% 指定参数个数的同时指定了首个参数的默认值，那么这个命令的
% 第一个参数就成为可选的参数（要使用中括号指定）
\newcommand\love[3][喜欢]{#2#1#3}

% 正文区（文稿区）
\begin{document}
	\PRC
	
	% 注意参数的对应关系
	\loves{猫儿}{鱼}

	\hatedby{猫儿}{萝卜}

	\love{猫儿}{鱼}
	\love[最爱]{猫儿}{鱼}

\end{document}


````

