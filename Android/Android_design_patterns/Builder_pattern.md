###  构建者模式  Builder pattern  


####   模式的定义  definition
将一个复杂对象的构建与它的表示分离，使得同样的构建过程可以创建不同的表示。  
如何理解这个定义呢？ --一般我们定义对象的时候，Object obj = new Object();是可以看到new这个过程的，  
构建者模式中创建对象的过程被封装起来，与对象的表示分开了。  

####   使用场景 Why to use   
主要用于构造一个复杂的对象，如参数非常多，且很多参数都有默认值。  

####  使用示例一   example1  
```java  
package org.tc.builder;

public class Builder {

	private Computer computer;
	
	public Builder () {
		computer = new Computer();
	}
	
	public Builder setBoard(String board) {
		computer.setBoard(board);
		return this;
	}
	
	public Builder setOs(String os) {
		computer.setOs(os);
		return this;
	}
	
	public Builder setDisplay(String display) {
		computer.setDisplay(display);
		return this;
	}
	
	public Computer create() {
		return computer;
	}	
}    

package org.tc.builder;
public class Computer extends Product {

	private String board;
	private String os;
	private String display;
	
	@Override
	public void setBoard(String board) {
		this.board = board;
	}

	@Override
	public void setOs(String os) {
		this.os = os;
	}

	@Override
	public void setDisplay(String display) {
		this.display = display;
	}

	@Override
	public String toString() {
		return "Board:" + this.board + ", " + "Display:" + this.display + ", " + "OS:" +  this.os;
	}
}   

package org.tc.builder;
public abstract class Product {

	public abstract void  setBoard(String board);
	public abstract void  setOs(String os);
	public abstract void  setDisplay(String display);
	
}  

package org.tc.builder;
public class TestBuilder {

	public static void main(String[] args) {
		Computer computer = new Builder().setBoard("ASUS").setOs("Windows").setDisplay("LG").create();
		System.out.println(computer);
	}
}
```  
  
运行结果：  
Board:ASUS, Display:LG, OS:Windows  


####  静态内部类实现   
```java   
package org.tc.builder;

public class Computer extends Product {

	private String board;
	private String os;
	private String display;
	
	
	public Computer() {}
	
	public Computer(String board2, String os2, String display2) {
		setBoard(board2);
		setOs(os2);
		setDisplay(display2);
	}

	@Override
	public void setBoard(String board) {
		this.board = board;
	}

	@Override
	public void setOs(String os) {
		this.os = os;
	}

	@Override
	public void setDisplay(String display) {
		this.display = display;
	}

	@Override
	public String toString() {
		return "Board:" + this.board + ", " + "Display:" + this.display + ", " + "OS:" +  this.os;
	}

	
	
	public static class BuilderInner {
		private String board;
		private String os;
		private String display;
		
		public Computer.BuilderInner setBoard(String b) {
			this.board = b;
			return this;
		}
		
		public Computer.BuilderInner setOs(String os) {
			this.os = os;
			return this;
		}
		
		public Computer.BuilderInner setDisplay(String display) {
			this.display = display;
			return this;
		}
		
		public Computer create() {
			return new Computer(this.board, this.os, this.display);
		}	
	}	
}

public class TestBuilder {

	public static void main(String[] args) {
		Computer computer = new Builder().setBoard("ASUS").setOs("Windows").setDisplay("LG").create();
		System.out.println(computer);
		
		// new implement  
		Computer com = new Computer.BuilderInner().setBoard("ASUS").setOs("Windows").setDisplay("LG").create();
		System.out.println(com);
	}
}

```  

###  总结   
*  优点： 良好的封装性；建造者独立，容易扩展。  
*  缺点： 会产生多余的Builder对象，消耗内存。  



  
