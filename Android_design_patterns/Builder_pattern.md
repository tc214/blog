###  构建者模式  Builder pattern  


####   模式的定义  definition
将一个复杂对象的构建与它的表示分离，使得同样的构建过程可以创建不同的表示。  
如何理解这个定义呢？ --一般我们定义对象的时候，Object obj = new Object();是可以看到new这个过程的，  
构建者模式中创建对象的过程被封装起来，与对象的表示分开了。  

####   使用场景 Why to use   
主要用于构造一个复杂的对象，如参数非常多，且很多参数都有默认值。  

####  使用示例   example  
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



  
