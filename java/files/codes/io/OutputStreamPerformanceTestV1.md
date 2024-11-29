```java
import java.io.FileOutputStream;
import java.io.IOException;

public class CreateFileV1 {

    public static void main(String[] args) throws IOException {
        FileOutputStream fos = new FileOutputStream(BufferedConst.FILE_NAME);
        long startTime = System.currentTimeMillis();

        for (int i = 0; i < BufferedConst.FILE_SIZE; i++) {
            fos.write(1);
        }
        fos.close();

        long endTime = System.currentTimeMillis();
        System.out.println("File created: " + BufferedConst.FILE_NAME);
        System.out.println("File size: " + (BufferedConst.FILE_SIZE / 1024 / 1024) + "MB");
        System.out.println("Time taken: " + (endTime - startTime) + "ms");
    }
}
```