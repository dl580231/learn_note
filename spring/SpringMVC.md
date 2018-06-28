#### 跟随SpringMVC的请求
![SpringMVC](https://github.com/dl580231/IMG/raw/master/Spring/SpringMVC.jpg)  
！！！等有时间了自己画一个符合描述的图，图来源于网络
1. DispatcherServlet（前端控制器）会通过查询处理器映射将请求交给指定的SpringMVC控制器
2. 处理器映射通过请求所携带的URL信息进行决策，确定请求的下一站
3. 确定了控制器之后，DispatcherServlet会将请求交给指定的控制器，请求卸下负载等待控制器的处理，控制器会将具体的业务逻辑交给M层去实现
4. 在控制器处理完成之后，会产生一些用于响应的信息，这些信息被称为模型，不能只将原始信息数据返回给用户，所以还会发送一个视图（视图名称），将模型视图打包发给DispatcherServlet，在这里使用视图名可以保证控制器不与具体的视图相耦合
5.  因为从控制器传来的只是一个视图名，DispatcherServlet会使用视图解析器匹配为一个特定的视图
6.  DispatcherServlet交付模型数据，实现视图
7.  视图使用模型视图输出，输出通过响应对象传递给客户端
#### 搭建SpringMVC
