# 基本文件操作

## 操作文件

```java
import java.io.File;
import java.io.FileReader;
import java.io.FileWriter;

public class Tests {
    public static void main(String[] args) {
        final String fileName = "newFile.txt";
        // create a file object for the current location
        File file = new File(fileName);
        try {
            // trying to create a file based on the object
            if (file.exists()) {
                System.out.println("The file already exists.");
            } else {
                if (file.createNewFile()) {
                    System.out.println("The new file is created.");
                }
            }
        } catch (Exception e) {
            e.printStackTrace();
        }

        final String data = "This is the data in the output file";
        // Creates a Writer using FileWriter
        try (FileWriter output = new FileWriter(fileName)) {
            // Writes string to the file
            output.write(data);
            System.out.println("Data is written to the file.");

            // Closes the writer
        } catch (Exception e) {
            e.printStackTrace();
        }

        char[] array = new char[128];
        try (FileReader input = new FileReader(fileName)) {
            // Creates a reader using the FileReader
            // Reads characters
            int len = input.read(array);
            System.out.print("Data in the file: ");
            System.out.println(new String(array, 0, len));
            // Closes the reader
        } catch (Exception e) {
            e.printStackTrace();
        }

        if (file.exists()) {
            if (file.delete()) {
                System.out.println("The file is deleted.");
            } else {
                System.out.println("The file is not deleted.");
            }
        } else {
            System.out.println("The file is not exists.");
        }
    }
}
```

## 检查目录

```java
import java.io.File;
import java.util.Deque;
import java.util.LinkedList;
import java.util.Objects;

public class Tests {
    public static void main(String[] args) {
        final String fileName = ".";
        File file = new File(fileName);
        if (file.isDirectory()) {
            for (File e : Objects.requireNonNull(file.listFiles())) {
                if (e.isDirectory())
                    System.out.println("isDirectory " + e.getName());
                else if (e.isFile())
                    System.out.println("isFile " + e.getName());
            }
        } else {
            System.out.println("isFile " + file.getName());
        }
        Deque<File> deque = new LinkedList<>();
        deque.offerFirst(file);
        int fileCount;
        while (deque.size() > 0) {
            File f = deque.pollLast();
            fileCount = 0;
            if (f.isFile())
                break;
            if (f.isDirectory()) {
                for (File e : Objects.requireNonNull(f.listFiles())) {
                    if (e.isFile())
                        fileCount++;
                    else if (e.isDirectory())
                        deque.offerFirst(e);
                }
                if (fileCount > 0)
                    System.out.printf("The directory '%s' file count is %d\n", f.getAbsolutePath(), fileCount);
            }
        }
    }
}
```

## 非阻塞方式

```java
import java.io.BufferedReader;
import java.io.BufferedWriter;
import java.io.IOException;
import java.nio.charset.StandardCharsets;
import java.nio.file.Files;
import java.nio.file.Path;
import java.nio.file.Paths;

public class Tests {
    public static void main(String[] args) {
        final String fileName = "newFile.txt";
        Path path = Paths.get(fileName);
        try {
            // Create the empty file with default permissions, etc.
            if (Files.exists(path))
                System.out.println("The file already exists.");
            else {
                Files.createFile(path);
                System.out.println("The new file is created.");
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
        String content = "This is the data in the output file";
        byte[] buf = content.getBytes(StandardCharsets.UTF_8);
        byte[] fileArray;
        try {
            Files.write(path, buf);
            fileArray = Files.readAllBytes(path);
            System.out.println(new String(fileArray));
        } catch (IOException e) {
            e.printStackTrace();
        }
        content = "This is the data in the output file with Buffer";
        try (BufferedWriter writer = Files.newBufferedWriter(path, StandardCharsets.UTF_8)) {
            writer.write(content, 0, content.length());
        } catch (IOException e) {
            e.printStackTrace();
        }
        try (BufferedReader reader = Files.newBufferedReader(path, StandardCharsets.UTF_8)) {
            String line;
            while ((line = reader.readLine()) != null) {
                System.out.println(line);
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
        try {
            Files.deleteIfExists(path);
            System.out.println("The file is deleted.");
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```