---
title: Razor syntax reference for ASP.NET Core
tags:
---
Razor is a markup syntax for embedding server-based code into webpages. The Razor syntax consists of Razor markup, C#, and HTML. Files containing Razor generally have a .cshtml file extension.

레이저는 웹페이지에 서버기반 코드를 내장하기 위한 마크업 구문입니다.
레이저 문법은 레이저 마크업, C#, 그리고 HTML로 구성됩니다. 레이저를 포함하는 파일들은 일반적으로 .cshtml 파일 확장자를 가지고 있습니다.

## Rendering HTML
The default Razor language is HTML. Rendering HTML from Razor markup is no different than rendering HTML from an HTML file. HTML markup in .cshtml Razor files is rendered by the server unchanged.

기본 레이저 언어는 HTML입니다. 레이저 마크업에서 HTML을 렌더링하는 것은 HTML 파일에서 HTML을 렌더링하는 것과 차이가 없습니다. .cshtml 레이저 파일에서 HTML 마크업은 변하지 않는 서버에 의해 렌더링 됩니다.

## Razor syntax
Razor supports C# and uses the @ symbol to transition from HTML to C#. Razor evaluates C# expressions and renders them in the HTML output.

레이저는 C#을 지원하고 HTML에서 C#으로 전환하기 위해 @기호를 사용합니다. 레이저는 C# 표현식을 평가하고 HTML 출력에서 렌더합니다.

When an @ symbol is followed by a Razor reserved keyword, it transitions into Razor-specific markup. Otherwise, it transitions into plain C#.

To escape an @ symbol in Razor markup, use a second @ symbol: