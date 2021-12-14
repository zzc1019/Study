## 接口测试

![image-20210808081246960](/Users/zhichaozhang/Library/Application Support/typora-user-images/image-20210808081246960.png)v

### 功能测试

### 异常测试

- 数据异常
  - 数据类型
  - Null ""
- 环境异常
  - 负载均衡
  - 集群热备

### 性能测试

- 负载测试
- 压力测试或强度测试
- 并发测试
- 稳定性测试或可靠性测试

## 自动化接口测试的范围

功能测试

数据异常测试

## 环境异常测试

nginx 负载均衡 宕机

### 自动化框架设计

<img src="/Users/zhichaozhang/Library/Application Support/typora-user-images/image-20210805222123130.png" alt="image-20210805222123130" style="zoom:50%;" />

## Git

<img src="/Users/zhichaozhang/Library/Application Support/typora-user-images/image-20210805223540575.png" alt="image-20210805223540575" style="zoom:50%;" />



克隆到本地

```bash
git clone git@github.com:zzc1019/AutoTest.git
```

进入文件夹并查看状态

```bash
cd AutoTest
vi test.txt
git status
```

将文件添加到暂存区

```bash
git add test.txt
git status -s
```

提交暂存区到本地仓库

```bash
git commit -m "msg"
```

直接从本地到本地仓库不经过暂存

```bash
git commit -a 
```

推到远程仓库

```bash
git push
```

查看本地分支

```bash
git branch
```

查看远程分支

```bash
git branch -a
```

创建新分支

```bash
git checkout -b branch1
```

本地分支删除

```bash
git branch -d branch1
# -D 强制删除
git branch -D branch1
```

远程分支删除

```bash
git branch -r -d origin/branch1
```

远程生效 **origin后一定要加空格**

```bash
git push origin :branch1
```

切换分支

```bash
git checkout master
```

合并分支到当前分支

```bash
git merge 需要合并到分支名
```

回退

```bash
# 回退1个
git reset --hard HEAD^
# 回退100个
git reset --hard HEAD～100
# 根据id回退到指定版本
git reflog
git reset --hard 6575a18
```

## TestNG



## HTTPClient

### post

- Post www-form-urlencoded

  ```java
  import java.util.ArrayList;
  import java.util.Iterator;
  import java.util.List;
  import java.util.Map;
  import java.util.Map.Entry;
  import org.apache.http.HttpEntity;
  import org.apache.http.HttpResponse;
  import org.apache.http.NameValuePair;
  import org.apache.http.client.HttpClient;
  import org.apache.http.client.entity.UrlEncodedFormEntity;
  import org.apache.http.client.methods.HttpPost;
  import org.apache.http.message.BasicNameValuePair;
  import org.apache.http.util.EntityUtils;
  /*
   * 利用HttpClient进行post请求的工具类
   */
  public class HttpClientUtil {
  	public String doPostForm(String url,Map<String,String> map,String charset){
  		HttpClient httpClient = null;
  		HttpPost httpPost = null;
  		String result = null;
  		try{
  			httpClient = new SSLClient();
  			httpPost = new HttpPost(url);
  			//设置参数
  			List<NameValuePair> list = new ArrayList<NameValuePair>();
  			Iterator iterator = map.entrySet().iterator();
  			while(iterator.hasNext()){
  				Entry<String,String> elem = (Entry<String, String>) iterator.next();
  				list.add(new BasicNameValuePair(elem.getKey(),elem.getValue()));
  			}
  			if(list.size() > 0){
  				UrlEncodedFormEntity entity = new UrlEncodedFormEntity(list,charset);
  				httpPost.setEntity(entity);
  			}
  			HttpResponse response = httpClient.execute(httpPost);
  			if(response != null){
  				HttpEntity resEntity = response.getEntity();
  				if(resEntity != null){
  					result = EntityUtils.toString(resEntity,charset);
  				}
  			}
  		}catch(Exception ex){
  			ex.printStackTrace();
  		}
  		return result;
  	}
  }
  ```

  

- Post Json

  ```java
  public class HttpClientUtil {
  	public String doPostJson(String url,Map<String,String> map,String charset){
  		HttpClient httpClient = null;
  		HttpPost httpPost = null;
  		String result = null;
  		try{
  			httpClient = new SSLClient();
  			httpPost = new HttpPost(url);
  			//设置参数
  			List<NameValuePair> list = new ArrayList<NameValuePair>();
  			Iterator iterator = map.entrySet().iterator();
        JSONObject data=new JSONObject();
  			while(iterator.hasNext()){
  				Entry<String,String> elem = (Entry<String, String>) iterator.next();
          data.put(elem.getKey(),elem.getValue());
        }
       //设置headers
        httpPost.setHeader("Content-Type","application/json");
        //将参数信息添加到方法
        StringEntity entity = new StringEntity(data.toString(),"utf-8");
        httpPost.setEntity(entity);
        
  			HttpResponse response = httpClient.execute(httpPost);
  			if(response != null){
  				HttpEntity resEntity = response.getEntity();
  				if(resEntity != null){
  					result = EntityUtils.toString(resEntity,charset);
            JSONObject resultJson = new JSONObject(result);
  				}
  			}
  		}catch(Exception ex){
  			ex.printStackTrace();
  		}
  		return resultJson;
  	}
  }
  ```

  

- Post file