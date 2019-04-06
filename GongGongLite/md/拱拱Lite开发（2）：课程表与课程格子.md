#拱拱Lite开发（2）：课程表与课程格子
上一次我们通过调用三翼的API获取了课程信息，现在，我们要设计控件呈现出数据。这次就来做一个简单的课程表吧
效果如下：

![课程表样式](https://github.com/StanleyOf427/Documents/blob/master/GongGongLite/img/2_1.png)

实现的方法有很多，我们这个就用自定义控件来吧：
##（1）创建自定义控件并编写界面
就用自定义控件来吧 这里我把格子(Block)和课表(CourseTable)分开了，方便日后添加创意，所以我们要建两个控件：BlockControl和CourseTableControl：右键项目-添加新项-用户控件，重命名（如果有多个控件的话，推荐放入同一个文件夹，方便管理）首先是BlockControl.xaml:
```xml
<UserControl

    x:Class="拱拱.Controls.BlockControl"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:local="using:拱拱.Controls"
    xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
    xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"

    mc:Ignorable="d"

   >
    <Button Name="GridButton"  x:FieldModifier="public" Opacity="0.4" Background="{x:Bind Color}" 

            HorizontalAlignment="Stretch" VerticalAlignment="Stretch"  Click="GridButton_Click"  

            Padding="0" BorderThickness="0">
        <Grid >
            <Grid.RowDefinitions>
                <RowDefinition Height="auto"/>
                <RowDefinition Height="auto"/>
                <RowDefinition Height="auto"/>
            </Grid.RowDefinitions>
            <TextBlock Text="{x:Bind CourseInf.Course}"  Grid.Row="0" FontSize="14" TextWrapping="Wrap"/>
            <TextBlock Text="{x:Bind CourseInf.Teacher}" Grid.Row="1" FontSize="10" TextWrapping="Wrap"/>
            <TextBlock Text="{x:Bind CourseInf.Location}" Grid.Row="2" FontSize="10" HorizontalAlignment="Center"/>
        </Grid>
    </Button>
</UserControl>
```
这个比较简单，只是一个Button里三行TextBlock,分别表示课程名、上课地点、教师。
然后是CourseTable.xaml:
```xml


<UserControl

    x:Class="拱拱.Controls.CoursesTable"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:local="using:拱拱.Controls"
    xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
    xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"

    mc:Ignorable="d"
    xmlns:weekDatemodel="using:拱拱.Models.CourseTableDate"

    >
    <Grid Name="ClassGrid" BorderThickness="0" HorizontalAlignment="Stretch" VerticalAlignment="Stretch" >
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="56"/>
            <ColumnDefinition Width="*"/>
            <ColumnDefinition Width="*"/>
            <ColumnDefinition Width="*"/>
            <ColumnDefinition Width="*"/>
            <ColumnDefinition Width="*"/>
            <ColumnDefinition Width="*"/>
            <ColumnDefinition Width="*"/>
        </Grid.ColumnDefinitions>
        <Grid.RowDefinitions>
            <RowDefinition Height="60"/>
            <RowDefinition Height="20"/>
            <RowDefinition Height="50"/>
            <RowDefinition Height="50"/>
            <RowDefinition Height="50"/>
            <RowDefinition Height="50"/>
            <!--6-->
            <RowDefinition Height="20"/>
            <RowDefinition Height="50"/>
            <RowDefinition Height="50"/>
            <RowDefinition Height="50"/>
            <RowDefinition Height="50"/>
            <RowDefinition Height="20"/>
            <!--12-->
            <RowDefinition Height="50"/>
            <RowDefinition Height="50"/>
            <RowDefinition Height="50"/>
            <!--15-->
            <RowDefinition Height="50"/>
            <RowDefinition Height="auto"/>
        </Grid.RowDefinitions>
        <Rectangle  Grid.Column="0" Grid.Row="0" Style="{StaticResource TopRectangelStyle}"/>
        <Rectangle Name="Rec1"  Grid.Column="1" Grid.Row="0" Style="{StaticResource TopRectangelStyle}"/>
        <Rectangle   Name="Rec2"  Grid.Column="2" Grid.Row="0" Style="{StaticResource TopRectangelStyle}"/>
        <Rectangle  Name="Rec3"  Grid.Column="3" Grid.Row="0" Style="{StaticResource  TopRectangelStyle}" x:FieldModifier="public"/>
        <Rectangle  Name="Rec4"  Grid.Column="4" Grid.Row="0" Style="{StaticResource  TopRectangelStyle}"/>
        <Rectangle Name="Rec5"   Grid.Column="5" Grid.Row="0" Style="{StaticResource TopRectangelStyle}"/>
        <Rectangle Name="Rec6"   Grid.Column="6" Grid.Row="0" Style="{StaticResource  TopRectangelStyle}"/>
        <Rectangle Name="Rec7"   Grid.Column="7" Grid.Row="0" Style="{StaticResource  TopRectangelStyle}"/>
        <TextBlock Grid.Column="0" Grid.Row="0" HorizontalAlignment="Center" VerticalAlignment="Top" FontFamily="Segoe MDL2 Assets" FontSize="14"></TextBlock>
        <TextBlock Grid.Column="0" Grid.Row="1" Style="{StaticResource LeftTextBlockStyle}">早读</TextBlock>
        <TextBlock Grid.Column="0" Grid.Row="2" Style="{StaticResource LeftTextBlockStyle}">1</TextBlock>
        <TextBlock Grid.Column="0" Grid.Row="3" Style="{StaticResource LeftTextBlockStyle}">2</TextBlock>
        <TextBlock Grid.Column="0" Grid.Row="4" Style="{StaticResource LeftTextBlockStyle}">3</TextBlock>
        <TextBlock Grid.Column="0" Grid.Row="5" Style="{StaticResource LeftTextBlockStyle}">4</TextBlock>
        <TextBlock Grid.Column="0" Grid.Row="6" Style="{StaticResource LeftTextBlockStyle}">中午</TextBlock>
        <TextBlock Grid.Column="0" Grid.Row="7" Style="{StaticResource LeftTextBlockStyle}">5</TextBlock>
        <TextBlock Grid.Column="0" Grid.Row="8" Style="{StaticResource LeftTextBlockStyle}">6</TextBlock>
        <TextBlock Grid.Column="0" Grid.Row="9" Style="{StaticResource LeftTextBlockStyle}">7</TextBlock>
        <TextBlock Grid.Column="0" Grid.Row="10" Style="{StaticResource LeftTextBlockStyle}">8</TextBlock>
        <TextBlock Grid.Column="0" Grid.Row="11" Style="{StaticResource LeftTextBlockStyle}">晚上</TextBlock>
        <TextBlock Grid.Column="0" Grid.Row="12" Style="{StaticResource LeftTextBlockStyle}">9</TextBlock>
        <TextBlock Grid.Column="0" Grid.Row="13" Style="{StaticResource LeftTextBlockStyle}">10</TextBlock>
        <TextBlock Grid.Column="0" Grid.Row="14" Style="{StaticResource LeftTextBlockStyle}">11</TextBlock>
        <TextBlock Grid.Column="0" Grid.Row="15" Style="{StaticResource LeftTextBlockStyle}">夜间</TextBlock>
        <TextBlock Grid.Row="0" Grid.Column="1" Style="{StaticResource TopTextBlockStyle}">一</TextBlock>
        <TextBlock Grid.Row="0" Grid.Column="1" Style="{StaticResource TopDateTextBlockStyle}" Text="{x:Bind TableDateModel.MonDate,Mode=OneWay}"  />
        <TextBlock Grid.Row="0" Grid.Column="2" Style="{StaticResource TopTextBlockStyle}">二</TextBlock>
        <TextBlock Grid.Row="0" Grid.Column="2" Style="{StaticResource TopDateTextBlockStyle}" Text="{x:Bind TableDateModel.TueDate,Mode=OneWay}" />
        <TextBlock Grid.Row="0" Grid.Column="3" Style="{StaticResource TopTextBlockStyle}">三</TextBlock>
        <TextBlock Grid.Row="0" Grid.Column="3" Style="{StaticResource TopDateTextBlockStyle}" Text="{x:Bind TableDateModel.WedDate,Mode=OneWay}" />
        <TextBlock Grid.Row="0" Grid.Column="4" Style="{StaticResource TopTextBlockStyle}">四</TextBlock>
        <TextBlock Grid.Row="0" Grid.Column="4" Style="{StaticResource TopDateTextBlockStyle}" Text="{x:Bind TableDateModel.ForDate,Mode=OneWay}" />
        <TextBlock Grid.Row="0" Grid.Column="5" Style="{StaticResource TopTextBlockStyle}">五</TextBlock>
        <TextBlock Grid.Row="0" Grid.Column="5" Style="{StaticResource TopDateTextBlockStyle}" Text="{x:Bind TableDateModel.FriDate,Mode=OneWay}" />
        <TextBlock Grid.Row="0" Grid.Column="6" Style="{StaticResource TopTextBlockStyle}">六</TextBlock>
        <TextBlock Grid.Row="0" Grid.Column="6" Style="{StaticResource TopDateTextBlockStyle}" Text="{x:Bind TableDateModel.SatDate,Mode=OneWay}" />
        <TextBlock Grid.Row="0" Grid.Column="7" Style="{StaticResource TopTextBlockStyle}">日</TextBlock>
        <TextBlock Grid.Row="0" Grid.Column="7" Style="{StaticResource TopDateTextBlockStyle}" Text="{x:Bind TableDateModel.SunDate,Mode=OneWay}" />
    </Grid>
</UserControl>
```
这里我用了一种简单粗暴的方式，直接Grid建立12行8列当安放格子的表。这里的StaticResource我就省了，大家可以按自己的想法来定义。
##（2）后台获取数据并安放格子
上面我是用x:Bind的方式传递数据，因此我们可以新建一个类CourseMode.cs,用于绑定数据作为数据模板：
```C#
namespace 拱拱.Models
{ 
    internal class CourseModel
    {

        public int Id;
        public string Course;
        public string Location;
        public string Teacher;
        public int Day;
        public string Week;
        public int Start;
        public int End;
    }
}
```
之后在BlockControl.xaml.cs里添加一个CourseModel的字段就行，我创建了一个叫CourseInf:
```C#
namespace 拱拱.Controls

{

    public sealed partial class BlockControl : UserControl

    {

        public static BlockControl Current;
        internal CourseModel CourseInf = new CourseModel ();
        internal Brush Color;

        public BlockControl ()
        {

            this.InitializeComponent ();

            Current = this;
        }

        public BlockControl (CourseModel model, Brush color) : this () //注意：如果未重载BlockControl()，会导致没有初始化控件,控件不显示。
        {

            CourseInf = model;

            Color = color;

        }

    }

}
```
最后就是为格子赋值并在CourseTable上安放格子喽：
```C#

namespace 拱拱.Controls

{

    public sealed partial class CoursesTable : UserControl

    {

        static int ArraySize = 50;

        BlockControl[] BlockArray = new BlockControl[ArraySize];//课程格子数组

        public static CoursesTable Current;

        int ThisWeekNum,WeekNum;

        internal CourseTableDateModel TableDateModel = new CourseTableDateModel();

 

        public CoursesTable()

        {

            this.InitializeComponent();

            Current = this;

            GetCourseBlocks();

            SetBlockColor();

	    SetBlockVisibility(1);//这里暂时简单以第一周课程为例

        }

 

        #region 课程格子设置

        private void GetCourseBlocks()//获取格子并传值

        {

            try

            {

                Brush[] Color = new Brush[] { new SolidColorBrush(Colors.Chocolate), new SolidColorBrush(Colors.BlueViolet),

                            new SolidColorBrush(Colors.CornflowerBlue), new SolidColorBrush(Colors.Cyan), new SolidColorBrush(Colors.DeepPink),

                            new SolidColorBrush(Colors.Gray), new SolidColorBrush(Colors.MediumSeaGreen),new SolidColorBrush(Colors.MediumSlateBlue),

                            new SolidColorBrush(Colors.Magenta), new SolidColorBrush(Colors.OliveDrab)};//10种格子颜色   

 

                /*数据库方式读取课程*/

                AppDatabase database = new AppDatabase();

                database.GetCreateConn();

                ObservableCollection<CourseModel> courses = new ObservableCollection<CourseModel>();

                courses = database.ReadData_Courses(courses);//courses为数据库中数据

                Random ran = new Random();//颜色随机数

 

                int i = 0;

                foreach (var course in courses)

                {

                    BlockArray[i] = new BlockControl(course, Color[ran.Next(0, 9)]);//传入格子信息

                    ClassGrid.Children.Add(BlockArray[i]);//把格子放在CourseTable上

 

		/*放置格子到正确的位置*/

                    if (course.Start <= 4)

                    {

                        BlockArray[i].SetValue(Grid.RowProperty, course.Start + 1);

                        BlockArray[i].SetValue(Grid.RowSpanProperty, course.End - course.Start + 1);

                    }

                    else if (course.Start >= 5 && course.Start <= 8)

                    {

                        BlockArray[i].SetValue(Grid.RowProperty, course.Start + 2);

                        BlockArray[i].SetValue(Grid.RowSpanProperty, course.End - course.Start + 1);

                    }

                    else if (course.Start >= 9)

                    {

                        BlockArray[i].SetValue(Grid.RowProperty, course.Start + 3);

                        BlockArray[i].SetValue(Grid.RowSpanProperty, course.End - course.Start + 1);

                    }

                    BlockArray[i].SetValue(Grid.ColumnProperty, course.Day);

		/*放置格子到正确的位置*/
 
                    i++;

                }

            }

            catch (Exception e)

            {

               //Debug操作，省略

            }

        }

 

        private void SetBlockVisibility(int weeknum)//根据周数设置课程格子可见性

        {

            for (int i = 0; BlockArray[i] != null; i++)

            {

                BlockArray[i].Visibility = Visibility.Collapsed;

                int[] weeks = WeekstringToint(BlockArray[i].CourseInf.Week);//把字符串Week变为类似"3,4,5，……，12"的数组，方便比较周数

                foreach (int weekin weeks)

                {

                    if (week == weeknum)

                    {

                        BlockArray[i].Visibility = Visibility.Visible;//周数相同，设置可见

                    }

                }
            } 
        }

 

        private void SetBlockColor()//相同课程设置颜色相同

        {

            for (int i = 0; BlockArray[i] != null; i++)

            {

                for (int j = i + 1; BlockArray[j] != null; j++)

                {

                    if (string.Equals(BlockArray[i].CourseInf.Course, BlockArray[j].CourseInf.Course))

                    {

                        BlockArray[j].Color = BlockArray[i].Color;

                    }

                }

            }

 

        }

        

        #endregion

 

 

    public int[] WeekstringToint(string WeekOri)//Week样式："3-12,17-20",返回int数组为"3,4,……,12,17,18,19,20"

        {

            int[] weeks = new int[30];

            string Week = WeekOri + "(";

 

            int a = 0, b = 0, c = 0, d = 0;//a,b用来计数,c和d分别为'-'左右的两数

 

            while (a < Week.Length)//遍历整个字符串

            {

                if ((int)Week[a] >= 48 && (int)Week[a] <= 57)//用数值的ASCII码值来判断是否为数字

                {

                    if ((int)Week[a + 1] >= 48 && (int)Week[a + 1] <= 57)//判断是否为两位数

                    {

                        c = ((int)(Week[a]) - 48) * 10 + (int)Week[a + 1] - 48;//c为转换后的数字

                        a += 2;//直接判断二位数字的下一个字符

                        continue;

                    }

                    //一位数

                    else c = (int)Week[a] - 48;

                }

 

 

                if ((int)Week[a] == 44)//判断是否为','

                {

                    weeks[b] = c;

                    while (weeks[b] < d)

                    {

                        b++;

                        weeks[b] = weeks[b - 1] + 1;//补全c,d之间的数，存到weeks

                    }

 

                    c = 0; d = 0; a++; b++;//重置c,d值

                    continue;

                }

                if ((int)Week[a] == 45)//判断是否为'-'

                {

                    if ((int)Week[a + 2] >= 48 && (int)Week[a + 2] <= 57)//判断是否为两位数

                    {

 

                        d = ((int)(Week[a + 1]) - 48) * 10 + (int)Week[a + 2] - 48;//d为转换后的数字

                        a += 3;//直接判断二位数字的下一个字符

                        continue;

                    }

                    else d = (int)Week[++a] - 48;

                    a++; continue;

                }

 

                if ((int)Week[a] == 40)//判断是否为'('

                {

                    weeks[b] = c;

                    while (weeks[b] < d)

                    {

                        b++;

                        weeks[b] = weeks[b - 1] + 1;//补全c,d之间的数，存到weeks

                    }

 

                    c = 0; d = 0; a++; b++;//重置c,d值

                    break;//判断完成，结束

                }

                a++;

            }

 

            return weeks;

        }

    }

}
```
这里我是创建控件数组来得到多个格子，而且方便之后对格子操作，只需后台操作对应的数组就行，十分方便。另外我是直接读取数据库，其实也可以直接用上一篇的保存到CourseModel的信息,直接赋值到courses。这样，一个简单的课表就完成了，当然，可以添加周数滑块、点击格子显示详细信息等功能，也可以添加动画，不过这里就不讲了，请按照自己的创意添加吧。