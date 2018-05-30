###  ������ģʽ  Builder pattern  


####   ģʽ�Ķ���  definition
��һ�����Ӷ���Ĺ��������ı�ʾ���룬ʹ��ͬ���Ĺ������̿��Դ�����ͬ�ı�ʾ��  
��������������أ� --һ�����Ƕ�������ʱ��Object obj = new Object();�ǿ��Կ���new������̵ģ�  
������ģʽ�д�������Ĺ��̱���װ�����������ı�ʾ�ֿ��ˡ�  

####   ʹ�ó��� Why to use   
��Ҫ���ڹ���һ�����ӵĶ���������ǳ��࣬�Һܶ��������Ĭ��ֵ��  

####  ʹ��ʾ��һ   example1  
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
  
���н����  
Board:ASUS, Display:LG, OS:Windows  


####  ��̬�ڲ���ʵ��   
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

###  �ܽ�   
*  �ŵ㣺 ���õķ�װ�ԣ������߶�����������չ��  
*  ȱ�㣺 ����������Builder���������ڴ档  



  
