# day2 - IO流

## 综合练习讲解

### 实现目录拷贝

**FileCopyUtils.java**

```java
/**
 * 文件拷贝工具
 * @author mrchai
 */
public class FileCopyUtils {

	/**
	 * 将一个源file对象拷贝到另一个目标file
	 * @param source  源文件(可能是目录)
	 * @param target  目标目录
	 * @throws IOException 
	 */
	public static void copy(File source,File targetDir) throws IOException{
		//判断当前需要被拷贝对象是目录还是标准文件
		if(source.isDirectory()){
			//在目录中创建子目录(不存在)
			targetDir = new File(targetDir,source.getName());
			if(!targetDir.exists()){
				//如果目录不存在则试图创建
				if(!targetDir.mkdirs()){
					throw new FileNotFoundException("目录创建失败,请检查权限");
				}
			}
			//读取目录
			File[] files = source.listFiles();
			if(Objects.nonNull(files)){				
				for (int i = 0; i < files.length; i++) {
					copy(files[i],targetDir);
				}
			}
		}else{
			//文件拷贝
			copyFile(source,targetDir);
		}
		
	}

	private static void copyFile(File source, File targetDir) throws IOException {
		BufferedInputStream bis = null;
		BufferedOutputStream bow = null;
		try{
			//获取源文件的输入流并包装为缓冲流
			bis = new BufferedInputStream(new FileInputStream(source));
			//根据源文件文件名组合目标目录成为新文件对象
			File target = new File(targetDir,source.getName());
			//获取目标文件的输出流
			bow = new BufferedOutputStream(new FileOutputStream(target));
			//声明字节缓冲区，缓存读取的字节数据
			byte[] b = new byte[1024];
			int len = 0;
			//循环读写
			while((len = bis.read(b)) != -1){
				bow.write(b, 0, len);
			}
		}finally{			
			//关闭资源
			if(bow != null)bow.close();
			if(bis != null)bis.close();
		}
	}
}
```

**测试类:**

```java
public class TestCopy {
	public static void main(String[] args) throws IOException {	
		//源file
		File f1 = new File("D:/javacode"); 
		//目标file
		File f2 = new File("C:/Users/mrchai/Desktop"); 
		//拷贝
		FileCopyUtils.copy(f1, f2);
	}
}
```



### 实现交易记录系统

**交易记录实体类：TransRecord.java**

```java
/**
 * 交易记录类
 * @author mrchai
 */
public class TransRecord {

	/**客户号*/
	private String num;
	/**姓名*/
	private String name;
	/**机构号*/
	private String jnum;
	/**性别*/
	private String sex;
	/**账号*/
	private String account;
	/**发生时间*/
	private Date time;
	/**发生金额*/
	private BigDecimal cash;
	
	public TransRecord() {
		// TODO Auto-generated constructor stub
	}
	
	public TransRecord(String num, String name, String jnum, String sex, String account, Date time, BigDecimal cash) {
		super();
		this.num = num;
		this.name = name;
		this.jnum = jnum;
		this.sex = sex;
		this.account = account;
		this.time = time;
		this.cash = cash;
	}


	public String getNum() {
		return num;
	}
	public void setNum(String num) {
		this.num = num;
	}
	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
	public String getJnum() {
		return jnum;
	}
	public void setJnum(String jnum) {
		this.jnum = jnum;
	}
	public String getSex() {
		return sex;
	}
	public void setSex(String sex) {
		this.sex = sex;
	}
	public String getAccount() {
		return account;
	}
	public void setAccount(String account) {
		this.account = account;
	}
	public Date getTime() {
		return time;
	}
	public void setTime(Date time) {
		this.time = time;
	}
	public BigDecimal getCash() {
		return cash;
	}
	public void setCash(BigDecimal cash) {
		this.cash = cash;
	}

	@Override
	public int hashCode() {
		final int prime = 31;
		int result = 1;
		result = prime * result + ((account == null) ? 0 : account.hashCode());
		result = prime * result + ((cash == null) ? 0 : cash.hashCode());
		result = prime * result + ((jnum == null) ? 0 : jnum.hashCode());
		result = prime * result + ((name == null) ? 0 : name.hashCode());
		result = prime * result + ((num == null) ? 0 : num.hashCode());
		result = prime * result + ((sex == null) ? 0 : sex.hashCode());
		result = prime * result + ((time == null) ? 0 : time.hashCode());
		return result;
	}

	@Override
	public boolean equals(Object obj) {
		if (this == obj)
			return true;
		if (obj == null)
			return false;
		if (getClass() != obj.getClass())
			return false;
		TransRecord other = (TransRecord) obj;
		if (account == null) {
			if (other.account != null)
				return false;
		} else if (!account.equals(other.account))
			return false;
		if (cash == null) {
			if (other.cash != null)
				return false;
		} else if (!cash.equals(other.cash))
			return false;
		if (jnum == null) {
			if (other.jnum != null)
				return false;
		} else if (!jnum.equals(other.jnum))
			return false;
		if (name == null) {
			if (other.name != null)
				return false;
		} else if (!name.equals(other.name))
			return false;
		if (num == null) {
			if (other.num != null)
				return false;
		} else if (!num.equals(other.num))
			return false;
		if (sex == null) {
			if (other.sex != null)
				return false;
		} else if (!sex.equals(other.sex))
			return false;
		if (time == null) {
			if (other.time != null)
				return false;
		} else if (!time.equals(other.time))
			return false;
		return true;
	}

	@Override
	public String toString() {
		return "TransRecord [num=" + num + ", name=" + name + ", jnum=" + jnum + ", sex=" + sex + ", account=" + account
				+ ", time=" + time + ", cash=" + cash + "]";
	}

}

```

**交易记录管理类：TransRecordManager.java**

```java
/**
 * 1.面向对象（封装，多态）
 * 2.常用类（Date,BigDecimal，DateFormat,SimpleDateFormat,String,File,Objects,Collections）
 * 3.异常处理
 * 4.新特性(lambda表达式，流式API)
 * 5.IO流
 * 
 * 交易记录管理类
 * @author mrchai
 *
 */
public class TransRecordManager{
   
	//声明成员变量用于存储所有的交易记录数据
	private List<TransRecord> records = new ArrayList<>();
	
    /**
    * 加载数据
    * @param in - 数据流
    * @return
    * @throws - 解析过程中IO错误
    */
    public void load(InputStream in) throws IOException{
    	//将获取的字节流转换为字符流并包装成缓冲流
        BufferedReader br = new BufferedReader(new InputStreamReader(in));
        String line = "";
        while((line = br.readLine()) != null){
        	//排除注释行（以“#”开头）
        	if(!line.startsWith("#")){
        		TransRecord record = parseDataToObj(line);
        		records.add(record);
        	}
        }
    }
    
    /**
     * 将一行文本数据解析为一个交易记录对象
     * @param line
     * @return
     */
    private TransRecord parseDataToObj(String line) {
		TransRecord tr = new TransRecord();
    	String[] data = line.split("\\|");
		tr.setNum(data[0]);
    	tr.setName(data[1]);
    	tr.setJnum(data[2]);
    	tr.setSex(Objects.equals(data[3],"1") ? "男" : "女");
    	tr.setAccount(data[4]);
    	tr.setCash(new BigDecimal(data[6]));
    	try {
			DateFormat fmt =new SimpleDateFormat("yyyyMMddHHmmss"); 
			Date time = fmt.parse(data[5]);
			tr.setTime(time);
		} catch (ParseException e) {
			e.printStackTrace();
		}
		return tr;
	}

	/**
    * 加载数据
    * @param fileName - 包含记录数据的文件名
    * @return
    * @throws - 解析过程中IO错误
    */
    public void load(String fileName) throws IOException{
    	File f = new File(fileName);
    	InputStream is = new FileInputStream(f);
    	load(is); 
    }

    /**
    * 取所有记录
    * @return 所有记录数组或null
    */
    public List<TransRecord> getAll(){
        return records;
    }

    /**
    * 按客户号查询记录
    * @param customerNumber - 客户号
    * @return 符合条件的记录数组或null
    */
    public List<TransRecord> findByCustomerNumber(String customerNumber){
        return records.stream()
        			  .filter(r->Objects.equals(r.getNum(), customerNumber))
        			  .collect(Collectors.toList());
    }

    /**
    * 按日期段查询记录
    * @param start - 开始日期
    * @param end - 结束日期
    * @return 符合条件的记录数组或null
    */
    public List<TransRecord> findByDate(String start, String end){
    	try {
			DateFormat fmt = new SimpleDateFormat("yyyyMMddHHmmss");
			long from = fmt.parse(start).getTime();
			long to = fmt.parse(end).getTime();
			
//			List<TransRecord> list = new ArrayList<>();
//			for(TransRecord tr : records){
//				long now = tr.getTime().getTime();
//				if(now >= from && now <= to){
//					list.add(tr);
//				}
//			}
//			return list;
			
			return records.stream()
					      .filter(r->{
					    	  long now = r.getTime().getTime();
					    	  return now>=from && now <=to;
					      })
					      .collect(Collectors.toList());
			
		} catch (ParseException e) {
			e.printStackTrace();
		}    	
        return null;
    }

    /**
    * 取得总金额
    * @return 总金额
    */
    public BigDecimal totalAmount(){
    	BigDecimal result = new BigDecimal(0); 
    	for (TransRecord tr : records) {
			BigDecimal cash = tr.getCash();
			result = result.add(cash);
		}
        return result;
    }

    /**
    * 按金额排序
    * @return 按金额升序排序的结果
    */
    public List<TransRecord> sortByAmount(){
    	Collections.sort(records, (r1,r2)->r1.getCash().compareTo(r2.getCash()));
        return records;
    }

    /**
    * 打印  
    * @param out - 输出流
    */
    public void print(OutputStream out){
        
    }
}
```



## Properties类

Properties类是JDK1.0中的一个用于表示属性对象的类，Properties类从Hashtable继承，因此内部元素的存储方式为键值对结构(Key-Value),Properties类通常用作于配置文件的编写与读取，具体使用方式如下：

1. 声明属性文件(jdbc.propeties):

   ```properties
   className=com.mysql.jdbc.Driver
   url=jdbc\:mysql\://127.0.0.1\:3306/test
   user=root
   password=123456
   maxWating=10000
   ```

2. 读取并解析properties文件:

   ```java
   public class TestProperties2 {
   
   	public static void main(String[] args) throws FileNotFoundException, IOException {
   		//创建一个Properties对象
   		Properties p = new Properties();
   		//将指定的输入流对象加载为属性对象(键值对结构存储)
   		p.load(new FileInputStream("src/jdbc.properties"));
   		
   		//根据属性名获取属性值
   		String cname = p.getProperty("className");
   		System.out.println(cname);//com.mysql.jdbc.Driver
   		String url = p.getProperty("url");
   		System.out.println(url);//jdbc:mysql://127.0.0.1:3306/test
   		String user = p.getProperty("user");
   		System.out.println(user);//root
   		String password = p.getProperty("password");
   		System.out.println(password);//123456
   		
           //向properties对象中添加元素
   		p.setProperty("maxActive", "10");
   		p.put("maxWating", "10000");
   		
   		//存储属性信息到指定的输出流对象
           //参数1：输出流对象
           //参数2：注释信息
   		p.store(new FileOutputStream("src/jdbc.properties"), "file change"); 
   		
   		//列出当前属性对象中所有元素(键值对结构)
   		p.list(System.out);
   	}
   }
   
   ```

   

## RandomAccessFile

RandomAccessFile类是java.io包中一个特别的类，简称随机访问文件流，这里的“随机”不是随机读取或者写入的意思，而是说可以使用该类实现在文件的任意位置进行读写操作；因此，该类既可以实现输入流(InputStream)相关功能，也能实现输出流(OutputStream)相关功能，另外RandomAccessFile类从DataInput和DataOutput接口实现，具备直接操作元数据的能力，RandomAccesFile操作文件包含两个构造器：

1. RandomAccessFile(File file,String mode)
2. RandomAccessFile(Stirng fileName,String mode)

以上构造器中mode参数为设置流的读写模式，可选值为以下四个:

1. **"r"    以只读方式打开流**
2. **“rw”  以读写方式打开流**
3. "rws" 基于“rw”还要求对文件的内容或元数据的每个更新都同步写入到底层存储设备
4. "rwd" 基于“rw”还要求对文件内容的每个更新都同步写入到底层存储设备

常用方法包含以下：

1. read() 从流中读取一个字节
2. read(byte[] b) 将流中读取的字节存储到字节数组
3. readInt() 从流中读取一个int数据
4. readDouble() 从流中读取一个double数据
5. readBoolean() 从流中读取一个boolean数据
6. readChar() 从流中读取一个char数据
7. ... 
8. write(int b)  写入字节到文件中
9. write(byte[] b) 写入字节数组到文件中
10. writeInt(int i) 以元数据的方式写入一个int值到文件中
11. writeDouble(double d) 以元数据的方式写入一个double值到文件中
12. writeBoolean(boolean b) 以元数据的方式写入一个boolean值到文件中
13. writeChar(int c) 以元数据的方式写入一个char值到文件中
14. ...
15. getFilePointer() 获取当前流中的文件指针
16. seek(long l) 跳过指定长度字节数之后发生下一个读取或者写入

**实例1:**

```java
public class RandomStreamDemo {

	public static void main(String[] args) throws IOException {
		
		//创建一个随机访问文件流
		RandomAccessFile raf = new RandomAccessFile("C:/Users/mrchai/Desktop/javacode/Dog.java", "r");
//		raf.read();
//		raf.read();
//		raf.read();
//		raf.read();		
//		//获取当前文件指针的位置
//		long pos = raf.getFilePointer();
//		System.out.println(pos);
		
//        raf.writeUTF(String)
        
//		byte[] b = new byte[1024];
//		int len = 0;
//		while((len = raf.read(b)) != -1){
//			System.out.println(new String(b,0,len));
//		}

	}

}

```

**实例2：**

```java

public class RandomStreamDemo2 {

	public static void main(String[] args) throws IOException {
		
		RandomAccessFile raf = new RandomAccessFile("C:/Users/mrchai/Desktop/a.txt", "rw");
//		raf.writeLong(10000L);//占用8字节
//		raf.writeInt(10);//占用4字节
//		raf.writeFloat(3.14f);//占用4字节
//		raf.writeDouble(3560.98);//占用8字节
//		raf.writeBoolean(true);//占用1bits
		
        //跳过指定个字节
		raf.seek(24);
//		float f = raf.readFloat();
		boolean f = raf.readBoolean();
		System.out.println(f);
	}

}
```



## 对象序列化

### 背景

​	在玩游戏时，比如单机类的角色扮演游戏，在玩到一定的级别和状态后如果需要关闭电脑(退出游戏)，如何能实现保存当前游戏的进度以及角色的实时状态？又比如：在电商系统中需要对当前被检索的商品缓存到本地文件存储起来，同时需要能够在适当时候从本地文件中重新查询出数据；又比如：如何实现将一个java对象通过网络进行传输，并且最大限度的保证对象数据的完整性？

### 概述

​	通过对以上问题分析得出，将一个对象的信息完整的存储到磁盘，网络或者其他存储介质中，并且能够在适当的时候完整的还原这个对象的信息，这些需求很常见的操作；因此在java-io中提供了一种对象序列化(Serializable)的技术,这种技术可以通过特殊的手段将一个java对象中包含的数据按照一定的组织格式存储到对应的存储介质(文件，网络)，并且可以通过反序列化技术将对象完整还原回来。

​	Java-IO中对象序列化的实现方式有两种：

1. 实现Serializable接口
2. 实现Externalizable接口

### Serializable接口

Serializable接口是一个标记型接口，该接口内部不包含任何未实现方法，接口的作用仅限于标记该类所对应的对象是可序列化的，具体使用如下：

1. 需要被序列化对象对应的类实现Serializable接口

   ```java
   /**
    * 玩家类
    * @author mrchai
    */
   public class Player implements Serializable{
   
   	/**
   	 * 生成一个随机的序列号,用于反序列化时的对象校验
   	 */
   	private static final long serialVersionUID = 1281206283763342806L;
   	
   	private int id;//id
   	private String name;//角色名
   	private String niceName;//称号
   	private int level;//等级
   	private double money;//游戏币
   	private int hp;//血量
   	private int mp;//魔法量
   	
   	public Player() {
   		// TODO Auto-generated constructor stub
   	}
   	
   	public Player(int id, String name, String niceName, int level, double money, int hp, int mp) {
   		super();
   		this.id = id;
   		this.name = name;
   		this.niceName = niceName;
   		this.level = level;
   		this.money = money;
   		this.hp = hp;
   		this.mp = mp;
   	}
   	
   	public int getId() {
   		return id;
   	}
   	public void setId(int id) {
   		this.id = id;
   	}
   	public String getName() {
   		return name;
   	}
   	public void setName(String name) {
   		this.name = name;
   	}
   	public String getNiceName() {
   		return niceName;
   	}
   	public void setNiceName(String niceName) {
   		this.niceName = niceName;
   	}
   	public int getLevel() {
   		return level;
   	}
   	public void setLevel(int level) {
   		this.level = level;
   	}
   	public double getMoney() {
   		return money;
   	}
   	public void setMoney(double money) {
   		this.money = money;
   	}
   	public int getHp() {
   		return hp;
   	}
   	public void setHp(int hp) {
   		this.hp = hp;
   	}
   	public int getMp() {
   		return mp;
   	}
   	public void setMp(int mp) {
   		this.mp = mp;
   	}
   
   }
   
   ```

   2. 使用ObjectOutputStream和ObjectInputStream实现对象序列化和反序列化

   ```java
   public class TestPlayer {
   
   	public static void main(String[] args) throws IOException, ClassNotFoundException {
   		
   		Player p = new Player(1, "渣渣辉", "嗜血狂魔", 88, 35000, 15000, 3500);
   		p.setMsg("等级提升!");
   		
           //序列化
   //		FileOutputStream fos = new FileOutputStream("src/savepoint.txt");
   //		//基于节点流获取一个对象输出流(完成对象序列化)
   //		ObjectOutputStream oos  = new ObjectOutputStream(fos);
   //		oos.writeObject(p);
   //		oos.flush();
   //		oos.close();
   		
           //反序列化
   		FileInputStream fis = new FileInputStream("src/savepoint.txt");
   		//基于节点流获取一个对象的输入流(完成反序列化)
   		ObjectInputStream bis = new ObjectInputStream(fis);
   		Object obj = bis.readObject();
   		p = (Player)obj;
   		System.out.println(p.getName());
   		
   	}
   
   }
   
   ```

 ### Externalizable接口

另一种对象序列化的方式是使用需要被序列化对象对应的类实现Externalizable，Externalizable是从Serializable接口继承而来，在其基础上新增了两个未实现方法：readExternal(ObjectInput in)和writeExternal(ObjectOutput out);通过对这两个方法的实现，可以自定义对那些必要的属性进行序列化和反序列化操作，具体使用如下：

1. 需要被序列化对象对应的类实现Externalizable接口,同时实现两个方法readExternal，writeExternal

   ```java
   public class User implements Externalizable{
   
   	private int id;
   	private String name;
   	private String pwd;
       
   	public User() {
   	}
   	
   	public User(int id, String name, String pwd) {
   		super();
   		this.id = id;
   		this.name = name;
   		this.pwd = pwd;
   	}
   
   	public int getId() {
   		return id;
   	}
   	public void setId(int id) {
   		this.id = id;
   	}
   	public String getName() {
   		return name;
   	}
   	public void setName(String name) {
   		this.name = name;
   	}
   	public String getPwd() {
   		return pwd;
   	}
   	public void setPwd(String pwd) {
   		this.pwd = pwd;
   	}
   
   	@Override
   	public void readExternal(ObjectInput input) throws IOException, ClassNotFoundException {
   		//指定需要被反序列化的属性并返回给成员变量
            id = input.readInt();
   		name = input.readUTF();
   	}
   
   	@Override
   	public void writeExternal(ObjectOutput output) throws IOException {
   		//指定需要被序列化的属性
            output.writeInt(id);
   		output.writeUTF(name);
   	}
   
   	@Override
   	public String toString() {
   		return "User [id=" + id + ", name=" + name + ", pwd=" + pwd + "]";
   	}
   	
   }
   
   ```

   ###transient关键字

   在对对象进行序列化操作时，可能由于某些属性为临时变量，并不需要被执行序列化操作，此时可以对相关属性使用transient关键字进行修饰即可，这样改属性就成为瞬时属性，在进行对象序列化操作时，该属性值不会被序列化，例如:

   ```java
   public class Player implements Serializable{
   
   	/**
   	 * 生成一个随机的序列号
   	 */
   	private static final long serialVersionUID = 1281206283763342806L;
   	
   	private int id;//id
   	private String name;//角色名
   	private String niceName;//称号
   	private int level;//等级
   	private double money;//游戏币
   	private int hp;//血量
   	private int mp;//魔法量
   	private transient String msg; //临时信息，不需要被序列化
   	//类变量也不允许被序列化
   	static int n = 100;
    
       //构造器定义（略）
       
       //setter/getter方法定义（略）
   }
   ```

   >注意事项：
   >
   >被static修饰的属性不再是实例变量(对象属性），直接属于类，因此被static修饰的属性也不能被序列化。



## 练习

1. 将一个99乘法表输出到文件中
2. 在一个目录下创建一个文本文件,并向文件里写入当前的创建时间,要求文件名的格式为yyyyMMddhhmmssSSS.txt
3. 将5个int和6个double数写入文件, 然后用另外一个程序读出并显示出来.
4. 从控制台输入数据，按如下格式保存到文件中，直到用户输入QUIT为止：   【2008-10-10 23:12:10】input your name pls(模拟的数据):   【2008-10-10 23:12:10】hello world:
5. 基于RandomAccessFile实现文件断点拷贝,要求实现在文件拷贝时，如果由于一些外在因素(断电，异常结束)导致拷贝终止，要求下一次拷贝时直接从上一次断开的位置进行拷贝，从而避免从头开始拷贝？（提示:getFilePointer()、seek()）
6. 基于对象序列化技术，完成一个小型电商管理系统商品模块，要求包含商品管理相关功能，其中商品类包含以下属性：id，商品名，类别名，单价，库存量，计量单位，上架时间，修改时间，销量，商品状态(1:正常销售/0:下架);根据以上提示信息，要求实现如下功能:
   1. 实现控制台输入商品信息，并记录到文件中
   2. 列表查询所有商品数据
   3. 按照不同的信息(价格，库存量，上架时间，销量)对商品排序显示
   4. 查询出所有下架商品
   5. 按类别查询出所有的商品
   6. 修改商品状态(要求同步更新修改时间)
   7. 修改商品的单价和库存量(要求同步更新修改时间)
   8. 删除下架商品

