>Knowledge is power                                ————Francis Bacon

##第一步，查询相关问题

![第一步：搜出相关问题](http://upload-images.jianshu.io/upload_images/1243238-4f9a4cb93eb368d5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##第二步，找到问题id、请求地址、请求参数

![第二步，找到问题id、请求地址、请求参数](http://upload-images.jianshu.io/upload_images/1243238-1826042aed6ad902.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##第三步，解析响应结果

![第三步，解析响应结果](http://upload-images.jianshu.io/upload_images/1243238-b957d09fcc60ce52.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##第四步，Talk is cheap.Show you my code

```
import java.util.concurrent.Executors
import groovy.json.JsonSlurper
import okhttp3.FormBody
import okhttp3.OkHttpClient
import okhttp3.Request
import okhttp3.Response
import org.jsoup.Jsoup

class KanMeizhi {

	static main(args) {

		def httpClient=new OkHttpClient()
		def questionId=40554112
		def threadPool=Executors.newFixedThreadPool(6)
		def dir = new File("H:/images")
		if(!dir.exists()){
			dir.mkdirs()
		}


		def downloadImageClosure={imgSrc->
			
			def fileName=imgSrc.substring(imgSrc.lastIndexOf('/')+1)+".jpg"
			def file=new File(dir, fileName)
			
			println "$fileName is being downloading ..."
			new FileOutputStream(file).withStream {outputStream->	
						
			    def inputStream=new URL(imgSrc).openStream()
				outputStream.write(inputStream.getBytes())
				
			}

			println "$fileName has been downloaded O(∩_∩)O！"
		}


		def parseImageSrcClosure={document->

			Jsoup.parse(document).getElementsByTag("img").findAll {img->

				img.attr("src").startsWith("http")
				
			}.collect {imgSrc->

				def src=imgSrc.attr("src")
				if(src.lastIndexOf("_")>0){
					return src.substring(0, src.lastIndexOf("_"))
				}
				return src
				
			}.unique().each { src->

				threadPool.execute(new Runnable(){
							void run() {

								downloadImageClosure(src)
							}
						})
			}
		}


		def offset=0
		def pageSize=20
		def total=0

		while(true){
			total++
			def parms="{\"url_token\":$questionId,\"pagesize\":$pageSize,\"offset\":$offset}"
			def formBody = new FormBody.Builder().add("_xsrf", "")
					.add("method", "next").add("params", parms).build();
			def request = new Request.Builder()
					.url("http://www.zhihu.com/node/QuestionAnswerListV2")
					.post(formBody).build()
			def response = httpClient.newCall(request).execute()

			if (!response.isSuccessful()) {

				throw new IOException("Unexpected code " + response)
			} else{

				def str=response.body().string()
				def json= new JsonSlurper().parseText(str)
				def msg=json?.msg
				if(msg !=null&& msg.size()>0){

					parseImageSrcClosure(msg.toString())
					offset+=pageSize
				}else{

					break
				}
			}
		}

		threadPool.shutdown()
		println "total page:$total"
	}
}
```
##第五步，No pictures . No Truth

![图片下载中](http://upload-images.jianshu.io/upload_images/1243238-54a211d76f0326d1.PNG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![部分图片](http://upload-images.jianshu.io/upload_images/1243238-d84453ecf7708e3f.PNG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![全部图片](http://upload-images.jianshu.io/upload_images/1243238-1db70a0a8d4a8e20.PNG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##参考文献

* http://square.github.io/okhttp/
* http://jsoup.org/
* http://www.groovy-lang.org/documentation.html
* [函数式编程思维](https://book.douban.com/subject/26587213/)
