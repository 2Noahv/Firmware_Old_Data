//
import java.awt.*;
import java.awt.event.*;
import java.text.ParseException;

import javax.swing.*;
import javax.swing.text.MaskFormatter;
			
			
	//외부 클래스 처리 방법
	//1. 
	//2. 
	//3. 
	
	public class LogineFields_Class extends JFrame{ 
				
		JPanel panel_c, panel_b, backpanel;
		JLabel label1, label2, label3;
		JTextField id, dataarea;
		JPasswordField password;
		JFormattedTextField data;
		JButton summit, cancel;
				
		
	
	public LogineFields_Class() throws ParseException {  
				
		summit = new JButton("확인");
		cancel = new JButton("취소");
		label1 = new JLabel("아이디", JLabel.CENTER);
		label2 = new JLabel("패스워드", JLabel.CENTER);
		label3 = new JLabel("생년월일", JLabel.CENTER);
							
		id=new JTextField(10);
		dataarea=new JTextField(20);
		password=new JPasswordField(10);
		MaskFormatter mf=new MaskFormatter("####-##-##   ");
		data=new JFormattedTextField(mf);
				
		panel_c = new JPanel();
		panel_b = new JPanel();
		backpanel = new JPanel();
				
		backpanel.setLayout(new GridLayout(0,2, 10, 10));
				
		backpanel.add(label1);
		backpanel.add(id);
		backpanel.add(label2);
		backpanel.add(password);
		backpanel.add(label3);
		backpanel.add(data);
				
		panel_c.add(summit);
		panel_c.add(cancel);
		panel_b.add(dataarea);
		
		
		//현재 클래스(new 외부 클래스(생성자 호출))
		//MyListener mylistener = new MyListener();
		summit.addActionListener(new LoginFields_inner(id, password, dataarea , panel_b)); 
		cancel.addActionListener(new LoginFields_inner(id, password, dataarea, panel_b));
				
		this.add("North", backpanel);
		this.add("Center", panel_c);
		this.add("South", panel_b);	
				
		setSize(300, 200);
		setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);			
		setVisible(true);
				
	}
				
		
		public static void main(String[] args) throws ParseException {
			new LogineFields_Class();
		}
	}
  
 
  

//외부 클래스 만들어야함
package ch02;

import java.awt.GridLayout;

import javax.swing.JFrame;
import javax.swing.JLabel;
import javax.swing.JPanel;
import javax.swing.JTextField;

public class MyMouseListener extends JFrame {
	
	JPanel panel, backpanel;
	JLabel name, age, job;
	JTextField Textname, Textage, Textjob;
	
	MyMouseListener() {
		
		backpanel = new JPanel();
		
		backpanel.setLayout(new GridLayout(0,2, 10, 10));
		
		panel = new JPanel();
		name = new JLabel("이름", JLabel.CENTER);
		
		
		panel = new JPanel();
		age = new JLabel("나이", JLabel.CENTER);
		
		
		panel = new JPanel();
		job = new JLabel("직업", JLabel.CENTER);
		
		Textname = new JTextField(10);
		Textage = new JTextField(10);
		Textjob = new JTextField(10);
				
		
		backpanel.add(name);
		backpanel.add(Textname);
		backpanel.add(age);
		backpanel.add(Textage);
		backpanel.add(job);
		backpanel.add(Textjob);
		
		this.add("North", name);
		this.add("Center", age);
		this.add("South", job);
		
		this.setTitle("입력칸");
		this.setVisible(true);
		this.setSize(500, 500);
		this.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
		
	
}

	
	
	
	
	
	
	public static void main(String[] args) {
		// TODO Auto-generated method stub
		new MyMouseListener();
	}

}


// 여기까지 만듬
package ch02;

import java.awt.event.MouseEvent;
import java.awt.event.MouseListener;

import javax.swing.JButton;
import javax.swing.JFrame;
import javax.swing.JLabel;
import javax.swing.JPanel;
import javax.swing.JScrollPane;
import javax.swing.JTable;
import javax.swing.table.DefaultTableModel;

public class JTabelTest extends JFrame {
		
		DefaultTableModel dtm;
		JTable table;
		JScrollPane Scroll;
		JLabel label;
		JPanel panel1, panel2;
		JButton but,but1,but2,but3;
		
		
	public JTabelTest() {
		
		panel1 = new JPanel();
		label = new JLabel("안녕 테이블~ ^^*");
		panel1.add(label);
		add(panel1,"North");
		
		String[] columnNames = {"이름", "나이", "직업"};
		Object[][] rowData = { {"홍길동", 13, "학생"},  {"김유신", 15, "화랑"}, {"김덕만", 16, "공주"}};
		
		dtm = new DefaultTableModel(rowData,columnNames);
		table = new JTable(dtm);
		Scroll = new JScrollPane(table);	//table 객체에 Title 표시 , 테이블 유효 높이 만큼만 표시
		add("Center",Scroll);
		
		
		panel2 = new JPanel();
		but = new JButton("추가");
		but1 = new JButton("삭제");
		but2 = new JButton("수정");
		but3 = new JButton("종료");
		panel2.add(but);
		panel2.add(but1);
		panel2.add(but2);
		panel2.add(but3);
		add(panel2,"South");
		
		MyMouseListener listener = new MyMouseListener();
		table.addMouseListener(listener);
		
		
		this.setTitle("JTable 테스트");
		this.setVisible(true);
		this.setSize(500, 500);
		this.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
	}
	
		
		 class MyMouseListener implements MouseListener {

			@Override
			public void mouseClicked(MouseEvent e) {
				// TODO Auto-generated method stub
				int srow = table.getSelectedRow(); // 선택된 행 인덱스
				
				Object name = table.getValueAt(srow, 0);
				Object age = table.getValueAt(srow, 1);
				Object job = table.getValueAt(srow, 2);
				
				label.setText("이름:" + name + ", 나이:"+  age + ", 직업:" + job);
			}

			@Override
			public void mousePressed(MouseEvent e) {
				// TODO Auto-generated method stub
				
			}

			@Override
			public void mouseReleased(MouseEvent e) {
				// TODO Auto-generated method stub
				
			}

			@Override
			public void mouseEntered(MouseEvent e) {
				// TODO Auto-generated method stub
			
			}

			@Override
			public void mouseExited(MouseEvent e) {
				// TODO Auto-generated method stub
				
			}
			 
		 }
	
		 

	
	
	
		public static void main(String[] args) {
			new JTabelTest();
		}
	}



