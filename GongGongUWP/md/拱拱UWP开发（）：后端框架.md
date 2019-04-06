#拱拱UWP开发（）：UI_后端框架

##（1）整体框架
这个App的后端主要包括与服务器通讯、数据保存、数据转换三个部分。

##（2）文件结构
[项目文件结构]()
这里我们将所有的URL及api账号密码及密钥放在了UriCollection.cs，其实不太建议这样做，容易被反编译盗取，不过因为安全需求不高，所以无所谓了。
然后我们将所有的服务器通讯部分放在Request.cs，便于统一调用。包含Get和Post两种方式，同时统一记录并传出session id，便于服务器端调用高速缓存，加快响应。
IApiInterface_Edu_Portal.cs则是信息门户和教务系统两种登陆方式的相关接口，之所以这里设计一个接口是因为教务系统登录与信息门户登录传出的是相同数据结构，这样便于统一调用实现。（请无视我这蹩脚的接口名称）
之后就是三个部分：StatusManager、Debug、DataManager,分别提供关于设备信息、错误记录、数据处理的部分。
其中DataManager中包含用于数据结构转换的DataAnalyse、关于数据库操作的DataBase、爬虫实现的备用接口Standby和其他所有调用服务器接口的具体实现。
Debug中比较单调，只是一个Bug的记录和日志。
StatusManager中则包含关于设备种类、地点和网络状况的信息。

##（3）
--- 
    using GongGongClassLibrary;
    using GongGongClassLibrary.DataManager;
    
       private class Test
        {
           //测试主接口
           private  async void Test_Edu_Portal() 
        {
            IApiInterface_Edu_Portal api_login; //主接口
            api_login = new GongGongClassLibrary.DataManager.Portal (); //接口实现，这里测试用信息门户
           
            var libPassword =
                api_login.Manager_GetLibPassword (2016888666, oasidjoa123); //获取图书馆密码
            var libResult = libPassword.Result; //获得结果
            if (libResult != null) //判断是否为空，防止空引用
            {
                if (libResult.Code == 0) //根据状态码判断是否成功
                {
                   //获取成功
        
                } else {
                   //获取失败
                }
        }
           
           
           
               //测试获得滚动图片栏图片
               private async void Test_FlipImage()
          {
                 var imglist = 
                 await GongGongClassLibrary.DataManager.FlipImages.Manager_FlipImages();//因为是静态成员，所以可以直接调用，很方便吧(￣▽￣)"
                 
                 if(imglist!=null)//判断是否为空，防止空引用
                 {
                     if(imglist.code==0)
                     {
                      foreach (var data in datalist.data)
                          {
                           //这里可以逐个处理得到的数据
                         }
                      }
                 }
            }
            
    }
