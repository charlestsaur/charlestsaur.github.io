# AI

超级简易的ai部署

## Install ollama

### 1. 通过 Homebrew 安装

如果你已经有了 Homebrew 了,可以使用这个命令会自动下载并安装Ollama及其所需的依赖项。

```sh
brew install ollama
```

安装完成后，你可以通过运行以下命令来验证安装是否成功：

```sh
ollama --version
```

如果成功，你会看到Ollama的版本号显示在终端中。此外，如果你想要安装Ollama的桌面版本（带有图形界面的应用程序），可以使用以下命令：

```sh
brew install --cask ollama
```

安装完成后，你可以直接启动Ollama并开始使用它来运行大型语言模型。

### 2. 去官网下载安装

[https://ollama.com](https://ollama.com)

[https://github.com/ollama/ollama](https://github.com/ollama/ollama)

## USE ollama

### 1. 下载模型

启动后,用以下指令下载模型

|        Model       	| Parameters 	|  Size 	|            Download            	|
|:------------------:	|:----------:	|:-----:	|:------------------------------:	|
| Gemma 3            	| 1B         	| 815MB 	| ollama run gemma3:1b           	|
| Gemma 3            	| 4B         	| 3.3GB 	| ollama run gemma3              	|
| Gemma 3            	| 12B        	| 8.1GB 	| ollama run gemma3:12b          	|
| Gemma 3            	| 27B        	| 17GB  	| ollama run gemma3:27b          	|
| QwQ                	| 32B        	| 20GB  	| ollama run qwq                 	|
| DeepSeek-R1        	| 7B         	| 4.7GB 	| ollama run deepseek-r1         	|
| DeepSeek-R1        	| 671B       	| 404GB 	| ollama run deepseek-r1:671b    	|
| Llama 3.3          	| 70B        	| 43GB  	| ollama run llama3.3            	|
| Llama 3.2          	| 3B         	| 2.0GB 	| ollama run llama3.2            	|
| Llama 3.2          	| 1B         	| 1.3GB 	| ollama run llama3.2:1b         	|
| Llama 3.2 Vision   	| 11B        	| 7.9GB 	| ollama run llama3.2-vision     	|
| Llama 3.2 Vision   	| 90B        	| 55GB  	| ollama run llama3.2-vision:90b 	|
| Llama 3.1          	| 8B         	| 4.7GB 	| ollama run llama3.1            	|
| Llama 3.1          	| 405B       	| 231GB 	| ollama run llama3.1:405b       	|
| Phi 4              	| 14B        	| 9.1GB 	| ollama run phi4                	|
| Phi 4 Mini         	| 3.8B       	| 2.5GB 	| ollama run phi4-mini           	|
| Mistral            	| 7B         	| 4.1GB 	| ollama run mistral             	|
| Moondream 2        	| 1.4B       	| 829MB 	| ollama run moondream           	|
| Neural Chat        	| 7B         	| 4.1GB 	| ollama run neural-chat         	|
| Starling           	| 7B         	| 4.1GB 	| ollama run starling-lm         	|
| Code Llama         	| 7B         	| 3.8GB 	| ollama run codellama           	|
| Llama 2 Uncensored 	| 7B         	| 3.8GB 	| ollama run llama2-uncensored   	|
| LLaVA              	| 7B         	| 4.5GB 	| ollama run llava               	|
| Granite-3.2        	| 8B         	| 4.9GB 	| ollama run granite3.2          	|

(ollama run ...其实是运行某个模型,但由你没有这个模型,所以ollama检测到后便会开始下载这个模型)

(参考 [https://github.com/ollama/ollama](https://github.com/ollama/ollama))

等待下载完成后即可使用

### 2. 图形化界面

在终端里使用并不太方便,所以我使用 Page Assist - A Web UI for Local AI Models 来搭配 ollama

(当然想这样的 AI GUI 并不少,按自己喜好来,它们不都是浏览器插件,也可能是个软件)

Page Assist - A Web UI for Local AI Models 属于浏览器插件

以下是它的支持情况

|   Browser   	| Sidebar 	| Chat With Webpage 	| Web UI 	|
|:-----------:	|:-------:	|:-----------------:	|:------:	|
| Chrome      	| ✅       	| ✅                 	| ✅      	|
| Brave       	| ✅       	| ✅                 	| ✅      	|
| Firefox     	| ✅       	| ✅                 	| ✅      	|
| Vivaldi     	| ✅       	| ✅                 	| ✅      	|
| Edge        	| ✅       	| ✅                 	| ✅      	|
| LibreWolf   	| ✅       	| ✅                 	| ✅      	|
| Zen Browser 	| ✅       	| ✅                 	| ✅      	|
| Opera       	| ❌       	| ❌                 	| ✅      	|
| Arc         	| ❌       	| ❌                 	| ✅      	|

下载方式就是去浏览器的插件商城里找到就行

如果你使用的是 Chrome 浏览器,那访问以下网址下载即可 

[https://chromewebstore.google.com/detail/page-assist-a-web-ui-for/jfgfiigpkhlkbnfnbobbkinehhfdhndo?hl=en-US&utm_source=ext_sidebar](https://chromewebstore.google.com/detail/page-assist-a-web-ui-for/jfgfiigpkhlkbnfnbobbkinehhfdhndo?hl=en-US&utm_source=ext_sidebar)
