```java
import java.io.FileInputStream;
import java.io.IOException;

public class ReadFileV2 {

    public static void main(String[] args) throws IOException {
        FileInputStream fis = new FileInputStream(BufferedConst.FILE_NAME);
        long startTime = System.currentTimeMillis();

        byte[] buffer = new byte[BufferedConst.BUFFER_SIZE];
        int fileSize = 0;
        int size;
        while ((size = fis.read(buffer)) != -1) {
            fileSize += size;
        }

        long endTime = System.currentTimeMillis();
        System.out.println("File created: " + BufferedConst.FILE_NAME);
        System.out.println("File size: " + (fileSize / 1024 / 1024) + "MB");
        System.out.println("Time taken: " + (endTime - startTime) + "ms");
    }
}
```