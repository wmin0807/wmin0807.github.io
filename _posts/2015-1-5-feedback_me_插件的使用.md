---
layout: post
title: feedback_me 插件的使用

---

This jQuery plug-in allows user to easily add an animatable UI widget with a feedback form which slides from the side of the screen.

---


feedback_me 在使用后的效果如下：

![1](https://cloud.githubusercontent.com/assets/5568742/5056362/a681f7e6-6cba-11e4-9eb2-bdcb2bf2d18d.png)
![2](https://cloud.githubusercontent.com/assets/5568742/5056363/c164806a-6cba-11e4-9f83-f38c9e18653a.png)

---

它会附着在窗口的一边，点击后会出现信息表，填写完成后点击提交即可，提交完成后feedbach_me 会将填写的信息通过post请求发送到你指定的url地址，你可以在后台写一个controller专门负责接收这个请求。这个插件的好处在于整个过程使用简单、有效，只需要通过简单的配置就能完成。

> 官方的文档的地址： http://plugins.jquery.com/feedback_me 
> github的地址： https://github.com/vedmack/feedback_me

##### 看一下如何使用它：

首先他是一个jQuery 插件，需要在一开始引入jQuery，然后引入它指定的js和css文件即可。

上图例子实现的代码：


	<link href="../../styles/jquery/jquery.feedback_me.css" rel="stylesheet" type="text/css">
	<script type="text/javascript" src="../../scripts/jquery/jquery.min.js"></script>
	<script type="text/javascript" src="../../scripts/jquery/plugin/jquery.feedback_me.js"></script>
	<script type="text/javascript">
		$(document).ready(function(){
    		fm_options = {
    			// 这个参数是设置这个反馈按钮在页面的哪个位置，left-bottom ...
        		position: "right-top",
        		// 是否显示邮箱
        		show_email: true,
        		email_label: "邮箱",
        		email_required: true,
        		// 单选框
       			show_radio_button_list: true,
       			radio_button_list_labels: ["1", "2", "3", "4", "5"],
       			radio_button_list_title: "给个好评哦...",
       			radio_button_list_required: false,
       			//
       			name_required: true,
        		name_label: "名字",
    			// 
    			message_label: "反馈信息",
    			message_required: true,
        		show_asterisk_for_required: true,
        		// 这个很重要，这是提交后信息发送到哪里
        		feedback_url: "http://.../feedback/message",
        		// 提交信息后提示
        		delayed_options: {
            		send_fail : "提交失败 :(. ",
           			 send_success : "提交成功，谢谢参与反馈，^_^ !"
        		},
        		submit_label: "提交",
    			// 页面按钮提示信息
    			trigger_label: "意见反馈"
    		};
    		//init feedback_me plugin
    		fm.init(fm_options);
		});
	</script>
