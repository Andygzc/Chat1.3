# Chat1.3
import java.awt.*;
import java.awt.event.*;
import java.io.*;
import java.net.Socket;
import java.net.SocketException;
import java.net.UnknownHostException;
import java.text.DateFormat;
import java.text.SimpleDateFormat;
import java.util.Date;
import java.awt.TextField;

public class ChatClient extends Frame {
	
	DataOutputStream dos = null;
	DataInputStream dis = null;
	boolean bconnected = false;
	Socket s = null;
	TextField tfTxt = new TextField();
	TextArea taContant = new TextArea();
	
	Thread tRecv = new Thread(new server());
	
	public static void main(String[] args) {
		new ChatClient().LaunchFrame();
	}
	
	

	public void LaunchFrame() {
		this.setLocation(300, 300);
		this.setSize(300, 400);
		this.setBackground(Color.blue);
		add(tfTxt,BorderLayout.SOUTH);
		add(taContant,BorderLayout.NORTH);
		pack();
		this.addWindowListener(new WindowAdapter(){

			public void windowClosing(WindowEvent arg0) {
				disconnected();
				System.exit(0);
			}
			
		});
		tfTxt.addActionListener(new TFListener());
		this.setVisible(true);
		connect();
		bconnected = true;
		tRecv.start();
	}
	
	class server implements Runnable {
		SimpleDateFormat format = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");

		public void run() {
			try {
				while(bconnected){
					String str = dis.readUTF();
					//System.out.println();
					taContant.setText(taContant.getText()  + "\n" + "----------------------------------" + format.format(new Date()) + "\n"+ str +"\n");
				}
			} catch (SocketException e){
				System.out.println("退出了，bye");
			} catch (IOException e) {
				e.printStackTrace();
			}
		}
		
	}
	
	public void connect() {
		try {
			s = new Socket("127.0.0.1",8888);
			dos = new DataOutputStream(s.getOutputStream());
			dis = new DataInputStream(s.getInputStream());
System.out.println("connected!");
		} catch (UnknownHostException e) {
			e.printStackTrace();
		} catch (IOException e) {
			e.printStackTrace();
		}
	}
	
	public void disconnected() {
		try {
			dis.close();
			dos.close();
			s.close();
		} catch (IOException e) {
			e.printStackTrace();
		}
	}
	
	
	
	private class TFListener implements ActionListener {

		public void actionPerformed(ActionEvent e) {
			String str = tfTxt.getText().trim();
			//taContant.setText(str);
			tfTxt.setText("");
			
			try {
				dos.writeUTF(str);
				dos.flush();
				//dos.close();
			} catch (IOException e1) {
				e1.printStackTrace();
			}
		}
		
	}

}
