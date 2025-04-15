```java
import java.util.*;

public class FileStorage {

    private final Set<String> files = new HashSet<>();

    // Add a file to the storage
    public boolean addFile(String path) {
        return files.add(normalize(path));
    }

    // Delete a file from the storage
    public boolean deleteFile(String path) {
        return files.remove(normalize(path));
    }

    // Copy a file from source path to destination directory
    public boolean copyFile(String fromPath, String toDirectory) {
        fromPath = normalize(fromPath);
        toDirectory = normalize(toDirectory);

        if (!files.contains(fromPath)) {
            return false; // Source file does not exist
        }

        if (!toDirectory.endsWith("/")) {
            return false; // Destination is not a directory
        }

        String fileName = fromPath.substring(fromPath.lastIndexOf("/") + 1);
        String targetPath = toDirectory + fileName;

        if (files.contains(targetPath)) {
            return false; // File already exists in target directory
        }

        files.add(targetPath);
        return true;
    }

    // Helper method to ensure consistent path formatting
    private String normalize(String path) {
        if (!path.startsWith("/")) {
            path = "/" + path;
        }
        return path.replaceAll("/+", "/"); // or just return path
    }

    // For debugging or inspection
    public void printFiles() {
        System.out.println("Current files:");
        files.stream().sorted().forEach(System.out::println);
    }


	// How the MAIN method would look
	public static void main(String[] args) {
        FileStorage storage = new FileStorage();

        List<List<String>> operations = List.of(
                List.of("ADD_FILE", "file_1"),
                List.of("ADD_FILE", "file_1"),
                List.of("ADD_FILE", "file_2"),
                List.of("ADD_FILE", "dir_1/file_2"),
                List.of("DELETE_FILE", "file_2"),
                List.of("DELETE_FILE", "file_2"),
                List.of("COPY", "/dir_1/file_2", "/"),
                List.of("COPY", "/file_2", "/"),
                List.of("COPY", "/file_3", "/dir_1/"),
                List.of("COPY", "/file_3", "/dir_3")
        );

        for (List<String> op : operations) {
            String type = op.get(0);
            boolean result = false;
            switch (type) {
                case "ADD_FILE" -> result = storage.addFile(op.get(1));
                case "DELETE_FILE" -> result = storage.deleteFile(op.get(1));
                case "COPY" -> result = storage.copyFile(op.get(1), op.get(2));
            }
            System.out.println(op + " => " + result);
        }

        storage.printFiles();
    }
	
}
```