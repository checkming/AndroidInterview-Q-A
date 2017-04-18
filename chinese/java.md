
	网络编程
		概述
		* A:计算机网络
			* 是指将地理位置不同的具有独立功能的多台计算机及其外部设备，通过通信线路连接起来，在网络操作系统，网络管理软件及网络通信协议的管理和协调下，实现资源共享和信息传递的计算机系统。
		* B:网络编程
			* 就是用来实现网络互连的不同计算机上运行的程序间可以进行数据交换。
		三要素
			IP
				每个设备在网络中的唯一标识
			端口号
				用于标识进程的逻辑地址
			协议
				网络中进行数据交换而建立的规则、标准或约定的集合
				* UDP
					* 数据要打包,包有大小在64K一下,面向无连接，数据不安全，速度快。不区分客户端与服务端。
				* TCP
					　　* 不需要打包,数据大小无限制,面向连接（三次握手），数据安全，速度略低。分为客户端和服务端。
					* 三次握手: 客户端先向服务端发起请求, 服务端响应请求, 传输数据
		通信原理
			* A:Socket套接字概述：
				* 网络上具有唯一标识的IP地址和端口号组合在一起才能构成唯一能识别的标识符套接字。
				* 通信的两端都有Socket。
				* 网络通信其实就是Socket间的通信。
				* 数据在两个Socket间通过IO流传输。
				* Socket在应用程序中创建，通过一种绑定机制与驱动程序建立关系，告诉自己所对应的IP和port。
		UDP传输
			* 1.发送Send
				* 创建DatagramSocket, 随机端口号
				* 创建DatagramPacket, 指定数据, 长度, 地址, 端口
				* 使用DatagramSocket发送DatagramPacket
				* 关闭DatagramSocket
			* 2.接收Receive
				* 创建DatagramSocket, 指定端口号
				* 创建DatagramPacket, 指定数组, 长度
				* 使用DatagramSocket接收DatagramPacket
				* 关闭DatagramSocket
				* 从DatagramPacket中获取数据
			* 3.接收方获取ip和端口号
				* String ip = packet.getAddress().getHostAddress();
				* int port = packet.getPort();
		代码
			接收端Receive
						//创建socket相当于创建码头
		DatagramSocket socket = new DatagramSocket(6666);	
		//创建packet相当于创建集装箱
		DatagramPacket packet = new DatagramPacket(new byte[1024], 1024);		
		
		while(true) {
				socket.receive(packet);//接收货物
			byte[] arr = packet.getData();
			int len = packet.getLength();
			String ip = packet.getAddress().getHostAddress();
			System.out.println(ip + ":" + new String(arr,0,len));
		}
			发送端Send
						//创建socket相当于创建码头
		DatagramSocket socket = new DatagramSocket();
		Scanner sc = new Scanner(System.in);
		
		while(true) {
			String str = sc.nextLine();
			if("quit".equals(str))
				break;
			//创建packet相当于创建集装箱
			DatagramPacket packet = 
					new DatagramPacket(str.getBytes(), str.getBytes().length, 			InetAddress.getByName("127.0.0.1"), 6666);
			socket.send(packet);//发货
		}
		socket.close();
	TCP协议
		* 1.客户端
			* 创建Socket连接服务端(指定ip地址,端口号)通过ip地址找对应的服务器
			* 调用Socket的getInputStream()和getOutputStream()方法获取和服务端相连的IO流
			* 输入流可以读取服务端输出流写出的数据
			* 输出流可以写出数据到服务端的输入流
		* 2.服务端
			* 创建ServerSocket(需要指定端口号)
			* 调用ServerSocket的accept()方法接收一个客户端请求，得到一个Socket
			* 调用Socket的getInputStream()和getOutputStream()方法获取和客户端相连的IO流
			* 输入流可以读取客户端输出流写出的数据
			* 输出流可以写出数据到客户端的输入流
		代码
			客户端
						Socket socket = new Socket("127.0.0.1", 9999);		//创建Socket指定ip地址和端口号
		InputStream is = socket.getInputStream();			//获取输入流
		OutputStream os = socket.getOutputStream();			//获取输出流
		BufferedReader br = new BufferedReader(new InputStreamReader(is));
		PrintStream ps = new PrintStream(os);
		
		System.out.println(br.readLine());
		ps.println("我想报名就业班");
		System.out.println(br.readLine());
		ps.println("爷不学了");
		socket.close();
			服务端
						ServerSocket server = new ServerSocket(9999);	//创建服务器
		Socket socket = server.accept();				//接受客户端的请求
		InputStream is = socket.getInputStream();		//获取输入流
		OutputStream os = socket.getOutputStream();		//获取输出流
		
		BufferedReader br = new BufferedReader(new InputStreamReader(is));
		PrintStream ps = new PrintStream(os);
		
		ps.println("开启it模式");
		System.out.println(br.readLine());
		ps.println("得了吧你");
		System.out.println(br.readLine());
		server.close();
		socket.close();
			多线程
					ServerSocket server = new ServerSocket(9999);	//创建服务器
		while(true) {
			final Socket socket = server.accept();	//接受客户端的请求
			new Thread() {
				public void run() {
					try {
						BufferedReader br = new BufferedReader(new InputStreamReader(socket.getInputStream()));
						PrintStream ps = new PrintStream(socket.getOutputStream());
						ps.println("开启暴走模式");
						System.out.println(br.readLine());
						ps.println("约不?");
						System.out.println(br.readLine());
						socket.close();
					} catch (IOException e) {
						e.printStackTrace();
					}
				}
			}.start();
		}
	}
