﻿#拱拱Lite开发（1）：调用三翼API实现登陆、课表获取、饭卡信息……

拱拱Lite是我UWP上的第一个作品，我也很乐意分享在开发时的收获，因此写下这一系列的文章，也算作一种积累吧，期望能有所帮助~

##（1）从官方文档开始

首先，我们来分析三翼给的API文档：

![Image text](https://github.com/StanleyOf427/Documents/blob/master/GongGongLite/img/1_1.png)

我们可以看到，这个API包含URL与参数，首先来看看如何传递这5个参数并获得数据。格式：URL + '?' + '参数名1' + '=' + '参数1' + '&' + '参数名2' + '=' + '参数2' +……实例：https://api.sky31.com/edu-new/course.php?role=sky31&hash=0267cc37887e5918c5ew21213&sid=012345781&password=666666&style=3&token=HTTP/1.1
（这里我就不贴上自己API的调用身份和密钥了哈，大家有需要可以在[三翼开发者中心](https://sky31.com)申请。）

我们看到，用上面的格式把要传递的参数分别填写上去，之间用'&'连接。这里记得是'https'而不是'http'（因为我们这个用了SSL加密，三翼官方文档该更新了……）之后把正确填好参数的URL用浏览器打开，看看传回的信息：

```JSON
{"code":0,"msg":"成功","data":{"1":{"2":[{"course":"工程经济","location":"兴教楼C401","teacher":"刘兴旺副教授","week":"9,10,11,12,13,14,15,16","week_string":"9-16(周)","section_length":"2","section_start":"3","section_end":"4"}],"3":[{"course":"化工原理Ⅲ","location":"逸夫楼-409","teacher":"李勇飞讲师（高校）","week":"1,2,3,4,5,6,7,8,9,10,11","week_string":"1-11(周)","section_length":"2","section_start":"5","section_end":"6"}],"4":[{"course":"环境工程学3","location":"逸夫楼-402","teacher":"陈跃辉讲师（高校）","week":"1,2,3,4,5,6,7,8,9,10,11,12","week_string":"1-12(周)","section_length":"2","section_start":"7","section_end":"8"}]},"2":{"1":[{"course":"概率论Ⅲ","location":"计算中心B202","teacher":"刘金龙","week":"1,2,3,4,5,6,7,8","week_string":"1-8(周)","section_length":"2","section_start":"1","section_end":"2"}],"2":[{"course":"环境监测Ⅱ","location":"一教楼-319","teacher":"汪形艳副教授","week":"1,2,3,4,5,6,7,8","week_string":"1-8(周)","section_length":"2","section_start":"3","section_end":"4"}]},"3":{"1":[{"course":"化工原理Ⅲ","location":"逸夫楼-409","teacher":"李勇飞讲师（高校）","week":"1,2,3,4,5,6,7,8,9,10,11","week_string":"1-11(周)","section_length":"2","section_start":"1","section_end":"2"}],"3":[{"course":"大学体育4","location":"体育馆","teacher":"谭新莉教授","week":"1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16","week_string":"1-16(周)","section_length":"2","section_start":"5","section_end":"6"}]},"4":{"1":[{"course":"环保装备腐蚀与防护","location":"一教楼-403","teacher":"陈洪波讲师（高校）","week":"9,10,11,12,13,14,15","week_string":"9-15(周)","section_length":"2","section_start":"1","section_end":"2"},{"course":"环保设备CAD技术","location":"计算中心B402","teacher":"陈洪波讲师（高校）","week":"1,2,3,4,5,6,7,8","week_string":"1-8(周)","section_length":"2","section_start":"1","section_end":"2"}],"3":[{"course":"环境监测Ⅱ","location":"一教楼-319","teacher":"汪形艳副教授","week":"1,2,3,4,5,6,7,8","week_string":"1-8(周)","section_length":"2","section_start":"5","section_end":"6"}],"4":[{"course":"概率论Ⅲ","location":"逸夫楼-110","teacher":"刘金龙","week":"1,2,3,4,5,6,7,8","week_string":"1-8(周)","section_length":"2","section_start":"7","section_end":"8"}],"5":[{"course":"化工原理Ⅲ","location":"逸夫楼-409","teacher":"李勇飞讲师（高校）","week":"1,2,3,4,5,6,7,8,9,10","week_string":"1-10(周)","section_length":"2","section_start":"9","section_end":"10"}]},"5":{"1":[{"course":"环保设备CAD技术","location":"计算中心B402","teacher":"陈洪波讲师（高校）","week":"1,2,3,4,5,6,7,8","week_string":"1-8(周)","section_length":"2","section_start":"1","section_end":"2"}],"2":[{"course":"环境工程学3","location":"逸夫楼-402","teacher":"陈跃辉讲师（高校）","week":"1,2,3,4,5,6,7,8,9,10,11,12","week_string":"1-12(周)","section_length":"2","section_start":"3","section_end":"4"}],"3":[{"course":"环保装备腐蚀与防护","location":"一教楼-403","teacher":"陈洪波讲师（高校）","week":"9,10,11,12,13,14,15","week_string":"9-15(周)","section_length":"2","section_start":"5","section_end":"6"}]}}}
```
如果能看到这一大坨的字符串（JSON），那么，恭喜你，API可以正常使用，我们可以继续。

##（2）用C#发送GET请求并得到JSON

刚才我们用浏览器打开URL并获得JSON，现在就需要用C#实现同样的功能，得到JSON。我用的是微软推荐的HttpWebRequest类：

```C#
public async static Task<string> GetJson(string ID, string Passwords)

{

var http = new HttpClient();

string url = String.Format("https://api.sky31.com/edu-new/course.php?role=201213121&hash=0267cc37887e59dqwe2312ddfwe1&sid={0}&password={1}&style=3&token=HTTP/1.1", ID, Passwords);

HttpWebRequest request = (HttpWebRequest)HttpWebRequest.Create(url); //创建一个请求示例

var response = await http.GetAsync(String.Format(url));//获取响应，即发送请求

var result = await response.Content.ReadAsStringAsync();

return result;

}
```
这样我们就实现了用C#发送GET请求并获得数据，下面，我们将要对我们获得的数据进行处理。

##（3）处理JSON并获得数据

下面，我们将要对 上面我们获得了JSON，但是我们在给用户呈现数据的时候肯定不能是这一大坨玩意，那么如何处理它呢？用字符串方法？NO，会累死人的。Json是具有特定格式的字串，C#中是有专门处理它的方法的：DataContractJsonSerializer.ReadObject(), 我们就用它吧

```C#


public class CourseManager

{public async static Task<RootObject> Manager(string ID, string Passwords)

{

var result = await GetJson(ID,Passwords);//result中存储的就是获得的JSON

using (var ms = new MemoryStream(Encoding.Unicode.GetBytes(result)))

{

DataContractJsonSerializer deseralizer = new DataContractJsonSerializer(typeof(RootObject));

RootObject model = (RootObject)deseralizer.ReadObject(ms);// //反序列化ReadObject

return model;

} 

}

 

[DataContract]
public class RootObject//对应{"code":0,"msg":"成功","data":{}}
{
[DataMember]
public int code { get; set; }

[DataMember]
public string msg { get; set; }

[DataMember]
public List<List<DataObject>> data { get; set; }//应为JSON里data数据是嵌套了两层，所以这里我们嵌套List
}

[DataContract]
public class DataObject//对应{"1":[{"course":"环保装备腐蚀与防护","location":"一教楼-403","teacher":"陈洪波讲师（高校）",……
{

[DataMember]
public string course { get; set; }

[DataMember]
public string location { get; set; }

[DataMember]
public string teacher { get; set; }

[DataMember]
public string week { get; set; }

[DataMember]
public string week_string { get; set; }

[DataMember]
public string section_length { get; set; }

[DataMember]
public string section_start { get; set; }

[DataMember]
public string section_end { get; set; }

[DataMember]
public int day { get; set; }
}
}
```

这里的[DataContract]和[DataMember]是用于标记属性，把JSON对应值传入。这样子，通过ReadObject()方法，我们就能把JSON数据对应存储到对应的类里，方便使用。但是，如果我们这样自己去一个一个分析JSON数据再写出对应的类，不仅低效，而且容易出错，所以我们可以去找对应的工具自动转化JSON为C#类（我推荐一个：[C#转JSON工具](http://www.bejson.com/convert/json2csharp/)）

##（4）最后一步：呈现数据
现在我们已经把获得的数据存入对应类的实例中了，最后要做的就剩下把数据呈现在控件上，可以直接赋值，也可以用数据绑定的方式。这里我把值传入到绑定到控件的CourseModel：

```C#

CourseManager.RootObject courses = await CourseManager.Manager(IDTextBox.Text, IDPasswordBox.Password);

Int n=0;

If(courses.code==0)

{

foreach (List<DataObject> TheDayCourse in classes.data)

{

if (TheDayCourse[n] != null)

{

foreach (var course in TheDayCourse)

{

var addCourse = new CourseModel()

{

Course = TheDayCourse[n].course,

Teacher = TheDayCourse[n].teacher,

Location = TheDayCourse[n].location,

Day = TheDayCourse[n].day,

Week = WeekstringCut(TheDayCourse[n].week_string),

Start = int.Parse(TheDayCourse[n].section_start),

End = int.Parse(TheDayCourse[n].section_end)

};

database.AddData(addCourse);

n++;

}

}

}
```

这样，正常情况下绑定的控件应该能收到数据更新，显示出数据。
