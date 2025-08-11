package socket2;

import java.io.DataInputStream;
import java.io.DataOutputStream;
import java.io.IOException;
import java.net.ServerSocket;
import java.net.Socket;
import java.util.Map;

import static log.LogClass.log;

public class ServerSocket1 {
public static void main(String[] args) throws IOException {
ServerSocket serverSocket = new ServerSocket(12345);
log("서버 소캣 시작 - 리스닝 포트: " + 12345);
// 연락이 와야 아래 로직 실행. block 되어서 기다리고 있습니다.
Socket socket = serverSocket.accept();
socket.setSoTimeout(5000);
socket.setTcpNoDelay(true);

        log("소켓 연결: " + socket);
        DataInputStream dis = new DataInputStream(socket.getInputStream());
        DataOutputStream dos = new DataOutputStream(socket.getOutputStream());

            //서버로 부터 문자 받기
            String received = dis.readUTF();
            log("client -> server: " + received);
            if (received.equals("refresh")){

            }


        //연결 종료시점은 보통 Servlet의 destroy() 메서드 실행시 자원 종료한다.
        log("연결 종료: " + socket);
        dis.close();
        dos.close();
        socket.close();
    }
}
