package socket2;

import java.io.DataInputStream;
import java.io.DataOutputStream;
import java.io.IOException;
import java.net.Socket;
import java.util.Scanner;

import static log.LogClass.log;

public class ClientSocket {

    private static final int PORT = 12345;

    public static void main(String[] args) throws IOException {
        log("클라이언트 시작");
        Socket socket = new Socket("localhost", PORT);
        DataInputStream dis = new DataInputStream(socket.getInputStream());
        DataOutputStream dos = new DataOutputStream(socket.getOutputStream());
        log("소켓 연결: " + socket);

        Scanner scanner = new Scanner(System.in);
        while (true) {
            log("전송 문자: ");
            String toSend = scanner.nextLine();

            //서버로 부터 문자 보내기
            dos.writeUTF(toSend);
            log("client -> server " + toSend);
            dos.flush();
            if (toSend.equals("exit")) {
                break;
            }

            //서버로 부터 문자 받기  -> 2번째것 부터 서버로 부터 받는 메세지가 안오면 여기서 blocking이 걸림
            String received = dis.readUTF();
            log("client <- server: " + received);
        }
        log("연결 종료: " + socket);
        dis.close();
        dos.close();
        socket.close();
    }
}
