# Null Object Pattern

# 널 오브젝트 패턴

* null 대신 사용할 수 있는 특별한 객체를 만들어, null로 인해 발생할 수 있는 예외 상황을 방지하고 코드의 간결성을 높이는 디자인 패턴이다.
* 코드에서 불필요한 조건문을 줄이고 객체의 기본 동작을 정의하는 데 유용하다.

# 널 오브젝트 패턴 예제

## 코드

```java
import java.util.*;

public class PlaylistMain {

    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);

        Playlist playlist = new Playlist();
        CommandManager commandManager = new CommandManager(playlist);

        while (true) {
            System.out.print("명령어를 입력해주세요: ");
            String input = scanner.nextLine();
            try {
                commandManager.execute(input);
            } catch (IllegalArgumentException e) {
                System.out.println(e.getMessage());
                break;
            }
        }
    }
}

class Playlist {
    private final List<String> musics = new ArrayList<>();

    public void add(String music) {
        musics.add(music);
    }

    public void remove(String music) {
        musics.remove(music);
    }

    public List<String> getMusics() {
        return new ArrayList<>(musics);
    }
}

class CommandManager {

    private static final String DELIMITER = "\\|";
    private final Map<String, Command> commands = new HashMap<>();
    private final Command defaultCommand = new DefaultPlaylistCommand();

    public CommandManager(Playlist playlist) {
        commands.put("/add", new AddPlaylistCommand(playlist));
        commands.put("/remove", new RemovePlaylistCommand(playlist));
        commands.put("/read", new ReadPlaylistCommand(playlist));
        commands.put("/exit", new ExitPlaylistCommand());
    }

    public void execute(String input) {
        String[] args = input.split(DELIMITER);
        String key = args[0];

        //NullObject Pattern
        Command command = commands.getOrDefault(key, defaultCommand);
        command.execute(args);
    }
}

interface Command {
    void execute(String[] args);
}

class AddPlaylistCommand implements Command {

    private final Playlist playlist;

    public AddPlaylistCommand(Playlist playlist) {
        this.playlist = playlist;
    }

    @Override
    public void execute(String[] args) {
        String music = args[1];
        playlist.add(music);
        System.out.println("플레이리스트에 추가되었습니다: " + music);
    }
}

class RemovePlaylistCommand implements Command {

    private final Playlist playlist;

    public RemovePlaylistCommand(Playlist playlist) {
        this.playlist = playlist;
    }

    @Override
    public void execute(String[] args) {
        String music = args[1];
        playlist.remove(music);
        System.out.println("플레이리스트에서 삭제되었습니다: " + music);
    }
}

class ReadPlaylistCommand implements Command {

    private final Playlist playlist;

    public ReadPlaylistCommand(Playlist playlist) {
        this.playlist = playlist;
    }

    @Override
    public void execute(String[] args) {
        List<String> musics = playlist.getMusics();
        for (int i = 0; i < musics.size(); i++) {
            System.out.println("플레이리스트" + (i + 1) + ": " + musics.get(i));
        }
    }
}

class ExitPlaylistCommand implements Command {

    @Override
    public void execute(String[] args) {
        throw new IllegalArgumentException("플레이리스트가 종료되었습니다.");
    }
}

class DefaultPlaylistCommand implements Command {

    @Override
    public void execute(String[] args) {
        System.out.println("잘못된 명령어입니다.");
    }
}
```

## 실행 결과

```text
명령어를 입력해주세요: /add|Whiplash
플레이리스트에 추가되었습니다: Whiplash
명령어를 입력해주세요: /add|Power
플레이리스트에 추가되었습니다: Power
명령어를 입력해주세요: /add|Sticky
플레이리스트에 추가되었습니다: Sticky
명령어를 입력해주세요: /read
플레이리스트1: Whiplash
플레이리스트2: Power
플레이리스트3: Sticky
명령어를 입력해주세요: /remove|Power
플레이리스트에서 삭제되었습니다: Power
명령어를 입력해주세요: /read
플레이리스트1: Whiplash
플레이리스트2: Sticky
명령어를 입력해주세요: /none
잘못된 명령어입니다.
명령어를 입력해주세요: /exit
플레이리스트가 종료되었습니다.
```