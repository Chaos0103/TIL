```java
import java.io.FileOutputStream;
import java.io.IOException;

public class CreateFileV4 {

    public static void main(String[] args) throws IOException {
        FileOutputStream fos = new FileOutputStream(BufferedConst.FILE_NAME);
        long startTime = System.currentTimeMillis();

        byte[] buffer = new byte[BufferedConst.FILE_SIZE];
        for (int i = 0; i < BufferedConst.FILE_SIZE; i++) {
            buffer[i] = 1;
        }
        fos.write(buffer);
        fos.close();

        long endTime = System.currentTimeMillis();
        System.out.println("File created: " + BufferedConst.FILE_NAME);
        System.out.println("File size: " + (BufferedConst.FILE_SIZE / 1024 / 1024) + "MB");
        System.out.println("Time taken: " + (endTime - startTime) + "ms");
    }
}
```