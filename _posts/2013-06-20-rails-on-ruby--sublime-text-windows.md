---
layout: post
title: "Rails on Ruby 与 Sublime Text 在Windows下的终极设置"
description: ""
category: 
tags: []
---
{% include JB/setup %}

最近开始学习Rails on Ruby，花了很多时间去调配sublime。在这里分享一下自己是如何设置sublime的，希望对大家能有用。

##安装Rails on ruby
因为我是用windows，在ror官网直接下的`windows Installer`,这个程序会帮你配置好你的Environment。这是目前就直接和方便的设置RoR环境的方法。


在`RailsInstaller`目录下能找到一个`Git Bash`的程序，这个是用来模拟linux环境用的，下文中的终端也是指这个，而不是windows的控制台。 



---------------------------------------

##安装Sublime Text插件

####1.Sublime Package Control
打开sublime console
>View>Show console

在里面输入下面的代码安装`Sublime Package Control`或者在[这里](http://wbond.net/sublime_packages/package_control/installation)查看更多的安装信息

    import urllib2,os; pf='Package Control.sublime-package'; ipp=sublime.installed_packages_path(); os.makedirs(ipp) if not os.path.exists(ipp) else None; urllib2.install_opener(urllib2.build_opener(urllib2.ProxyHandler())); open(os.path.join(ipp,pf),'wb').write(urllib2.urlopen('http://sublime.wbond.net/'+pf.replace(' ','%20')).read()); print('Please restart Sublime Text to finish installation')

####2.[sublime-rails-snippets](https://github.com/tadast/sublime-rails-snippets)
按住`Ctrl+Shift+p`输入`Install Package`然后搜索并安装`sublime-rails-snippets`

现在Ruby已经更新到1.9版本，Rails也到了3.2，sublime中的snippets已经很旧了我们需要这个新的插件来取代就sublime默认的snippets。

当安装完成后把Packages目录下的Rails文件夹删除就可以了。 

snippets是我最喜欢的一个功能！

![][img]

####3.[RubyFormat](https://github.com/zmbacker/RubyFormat.git)
按住`Ctrl+Shift+p`输入`Install Package`然后搜索并安装`ruby format`

这是个我自认为很好用的插件，在写ruby 代码时在`Go Anything`里输入`ruby_format`就能自动整理代码

##RSpec
要学ror首先得先得学习ruby，在sublime里学习ruby是一个很好的选择因为它本身就支持ruby, 写好code后按 `ctrl+b`就能直接运行。这里说的是如何让sublime支持RSpec. 

####安装
我用的是windows, 用`RailsInstaller`直接安装的，里是自带Rspec的。如果没有找到可以在终端输入

>`gem install rspec`


正常使用RSpec的方法是像下面这样。我们有两个文件`example.rb`和`example_spec.rb`如下

>`example.rb`

```ruby
def linker(link, text)
  "<a href=\"#{link}\">#{text}</a>"
end
```

>`example_spec.rb`

```ruby
require './example'	
describe "linker" do
	 it "gives you HTML ready link" do
	   linker("http://www.google.com", "Google").should eq("<a href=\"http://www.google.com\">Google</a>")
	 end
end
```

我们要在命令台输入
	
	$ rspec example_spec.rb
	.
	
	Finished in 0.00085 seconds
	1 example, 0 failures


有sublime就比较简单，我们可以把两个文件并在一起然后加上`rspec`的路径例如
>example.rb

	require `rspec/autorun`

	def linker(link, text)
	  "<a href=\"#{link}\">#{text}</a>"
	end
	
	describe "linker" do
	  it "gives you HTML ready link" do
	    linker("http://www.google.com", "Google").should eq("<a href=\"http://www.google.com\">Google</a>")
	  end
	end
	
然后按`ctrl+b`就可以直接运行。

windows系统需要把

	require `rspec/autorun

替换成 

    require 'X:/X/RailsInstaller/Ruby1.9.3/lib/ruby/gems/1.9.1/gems/rspec-core-2.13.1/lib/rspec/autorun'

X是你安装RailsInstaller的路径。

-----------------------------------

##其他
再用sublime时会遇到一些小问题，下面是我如何修复的

####1.Rails snippet 不显示候选项

当编辑`*.html.erb`时，直接输入`ff`一类的tabTrigger，ST并不会显示候选项，但是敲击tab缺也能激活相对应的snippet。 需要在`<% %>`里输入才能激活候选项。

这是因为rails.erb一类的文件会先把代码当做html文件处理，在`<% %>`里才会把它视为rails的代码。 这样用起来会让人感觉很困惑，因为很多snippet里是包括了`<% %>`的。 所以我们需要做以下修改。

点击`Preferences->Setting-User`打开用户自定义文件，并在开头`{`后加入以下代码

	"tab_completion": true,
	"auto_complete": true,
	"auto_complete_commit_on_tab": true,
	"auto_complete_with_fields": true,
	"auto_complete_selector": "text, source - comment",

这样自动补全功能就能用了。

####2.增加`<%= %>` rails 快速输入

`<%= %>`这个符号在rails里用的很多，每次输入都会很麻烦。`<%`是特殊符号,想制作Snippet也很麻烦。所以我们可以用`Key Binding`来解决. 

打开 `Preferences -> Key Bindings-User`, 并在`[`下加入以下代码：

	//short cut for <% %>
	{
		"args": {"contents": "%= $0 %>"},

		"command": "insert_snippet", "context": [
		{
			"key": "selector", 
			"match_all": true, 
			"operand": "source.ruby, text.html.ruby, text.haml", 
			"operator": "equal"
		},
		{ 
			"key": "preceding_text",
			"operator": "regex_match",
			"operand": ".*<",
			"match_all": true }
		],
		"keys": ["%"] 
	}
这样，输入`<%` 系统就会自动补全 为 `<%= %>`,同时光标也会停在正中

![][img2]

除此外还可以通过[快捷键+snippet](http://webtempest.com/sublime-text-2-how-to-create-snippets/ "学习制作Sublime Snippets")来解决，这个也是一个教你如何制作snippet的教程。 

####3.Toggle Comment Bug for Ruby

在ST中，`Ctrl+/` 和`Ctrl+Shift+/`都是注释开关

>`Ctrl+/`

![][imgComment]


>`Ctrl+Shift+/`

![][imgCommentP]

但在ruby中，段落注释的开关有一个bug，如下

![][imgCommentPError]

当时我找这个bug找了很久，最后发现是一个非常小的错误。
修复这个bug很简单

只需下载文件[Comments.tmPreferences][Comments.tmPreferences]并储存到Sublime Text 的 User 文件夹下，例如在windows下的目录为：

	C:\Users\UserName\AppData\Roaming\Sublime Text 2\Packages\User

-------------------------------------------

除此外我也把自己sublime[个人设置放在了github](https://github.com/duqcyxwd/Sublime_Text_Personal_Setting_and_Snippets)上，大家有兴趣的可以下来看看。 里面还有我其他语言的snippet

[img]: https://dl.dropboxusercontent.com/u/58367953/Picture%20Source/snippet_example.gif
[img2]:  https://dl.dropboxusercontent.com/u/58367953/Picture%20Source/rails_special_char_input.gif
[imgComment]: https://dl.dropboxusercontent.com/u/58367953/Picture%20Source/comment_toggle.gif
[imgCommentP]:https://dl.dropboxusercontent.com/u/58367953/Picture%20Source/comment_paragraph_toggle.gif
[imgCommentPError]:https://dl.dropboxusercontent.com/u/58367953/Picture%20Source/comment_paragraph_toggle_error.gif
[Comments.tmPreferences]: https://dl.dropboxusercontent.com/u/58367953/Picture%20Source/Comments.tmPreferences
