---
title: webUploader常用功能
date: 2019-05-06 23:14:14
tags:
---

# 建一个图片上传
<!--more-->
	var uploader=WebUploader.create({
		auto:false,		//是否开启选择图片后自动上传,true:开启,false:关闭
		server:'upload.do',	//上传接口
		dnd:'#fileList',		//指定区域可以将图片拖拽进去，多区域用,隔开
		paste:document.body, //指定区域通过粘贴添加截屏的图片，建议设置为document.body
		pick:{
			id:'#filePicker',		//选取按钮变为选择图片按钮
			label:'点击选择图片',	//按钮文字
			multiple:true		//是否开启多图选择
		},

		accept:{							//指定接收哪些类型文件
			title:'Images',					//文字描述
			extensions:'gif,jpg,jpeg,bmp,png',	//允许的文件后缀，不带点，用逗号分开
			mimeTypes:'image/*'			//？？？
		},

		thumb:{					//配置生成缩略图的选项
			width:100,
			height:100,
			qualiity:70,			//图片质量，只有type为image/jpeg的时候才有效
			allowMagnify:false,		//是否允许放大，生成小图不失真请选择false
			crop:false,			//是否允许裁剪
			type:'image/jpeg'		//为空的话保留原有图片格式，否则强制转成指定格式
		},

		compress:{					//配置压缩图片的选项，如果为false，则上传前不进行压缩
			width:1600,				
			height:1600,
			quality:50,				//图片压缩质量，只有type为image/jpeg的时候才有效
			allowMagnify:false,			//是否允许放大，生成小图不失真请选择false
			crop:false,				//是否允许裁剪
			preserveHeaders:true,		//是否保留头部meta信息
			noCompressIfLarger:false,	//如果压缩后文件比原文件大则使用原图，可能影响图片自动纠正
			compressSize:0			//单位字节，如果图片大小小于此值，不会采取压缩
		},

		prepareNextFile:true,	//允许文件上传提前把下一个文件准备好，比如需要图片压缩，md5序列化
		fileVal:file	,	//设置文件上传域的name,MultipartFile类型去接收

		method:'POST',	//文件上传方式：POST或者GET

		fileNumLimit:2,	//限定上传数量

		fileSizeLimit:1024*1024,		//限制总大小 1024*1024=1MB,但是这里设定不准确

		fileSingleSizeLimit:1024,		//限制单个文件大小	1024=1KB

	})
***
# WebUpLoader事件
**事件详情访问WebUpLoader API。下面举一个例子**
uploadStart	在某个文件开始上传前触发，一个文件只触发一次。
**如何调用**

	uploader.onUploadStart=function(file){
		alert("开始上传");
	}
**或者**

	uploader.on('uploadStart',function(file){
		alert("开始上传啦");
	});
***
# 获取或者设置配置项（option）

	uploader.option('thumb',{	//获取缩略图thumb配置
		crop:true				//此时缩略图被裁剪		长宽设置貌似有优先级
	});
***
# getStats
**获取文件统计信息。返回信息如下：**
+ successNum		上传成功的文件数
+ progressNum	上传中的文件数
+ cancelNum		被删除的文件数
+ invalidNum		无效的文件数
+ uploadFailNum	上传失败的文件数
+ queueNum		还在队列中的文件数
+ interruptNum	被暂停的文件数

**例子**

	uploader.on('uploadComplete',function(file){
		alert(uploader.getStats().successNum);		//上传完成时弹出上传成功的文件数
	});
***
# addButton
**添加文件选择按钮，如果一个按钮不够，需要调用此方法来添加。参数跟 pick一致**

	uploader.addButton({
		id:"#btn",
		innerHtml:'选择文件'
	});
***
# makeThumb
**生成缩略图，此过程为异步，所以需要传入callback。通常情况在图片加入队里后调用此方法来生成预览图。**
makeThumb(file,callback)
makeThumb(file,callback,width,height)
callback中可以接收两个参数
+ error，如果生成缩略图有错误，此error将为真
+ ret，缩略图的Data URL值	在IE6/7中不支持

**例子**

	uploader.on('fileQueued', function(file) {				//文件添加后触发
		var $li = $('<div id="'+file.id+'"class="file-item thumbnail">'
				+ '<img>' + '<div class="info">' + file.name + '</div>'
				+ '</div>'), $img = $li.find('img');
		$("#fileList").append($li);

		uploader.makeThumb(file, function(error, src) {				//生成缩略图	
			if (error) {
				$("#fileList").replaceWith('<span>不能预览</span>');
				return;
			}
			$img.attr('src', src);
		}, 100, 100);
	});
***
# addFiles
**添加文件到队列**
addFiles(file)
addFiles([file1,file2...])
***
# removeFile
**移除某一文件，默认只会标记文件状态已取消，如果第二个参数为true则会从queue（队列）中移除**
***
# retry
**重试上传，重试指定文件，或者出错的文件重新上传**
retry()
retry(file)
***
# upload
**开始上传，可以在暂停后调用继续上传**
upload()
upload(file|fileId)
***
# stop
**暂停上传**
stop()
stop(true)
stop(file)

