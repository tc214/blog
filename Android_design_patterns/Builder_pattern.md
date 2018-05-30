###  ������ģʽ  Builder pattern  


####   ģʽ�Ķ���  definition
��һ�����Ӷ���Ĺ��������ı�ʾ���룬ʹ��ͬ���Ĺ������̿��Դ�����ͬ�ı�ʾ��  
��������������أ� --һ�����Ƕ�������ʱ��Object obj = new Object();�ǿ��Կ���new������̵ģ�  
������ģʽ�д�������Ĺ��̱���װ�����������ı�ʾ�ֿ��ˡ�  

####   ʹ�ó��� Why to use   
��Ҫ���ڹ���һ�����ӵĶ���������ǳ��࣬�Һܶ��������Ĭ��ֵ��  

####  ʹ��ʾ��   example  
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



  
