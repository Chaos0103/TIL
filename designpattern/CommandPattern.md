# Command Pattern

# 커맨드 패턴

* 행동 디자인 패턴 중 하나로, 요청을 독립적인 객체로 변환해서 처리한다.

## 커맨드 패턴의 특징

* **분리**: 작업을 호출하는 객체와 작업을 수행하는 객체를 분리한다.
* **확장성**: 기존 코드를 변경하지 않고 새로운 명령을 추가할 수 있다.

## 커맨드 패턴의 장점

* 확장성
  * 새로운 커맨드를 쉽게 추가할 수 있다.
  * 새로운 커맨드를 추가하고 싶다면, 새로운 Command의 구현체만 만들면 된다.
  * 기존 코드를 대부분 변경할 필요가 없다.
* 분리
  * 작업을 호출하는 객체와 작업을 수행하는 객체가 분리되어 있다.
  * 각 작업마다 호출하는 if 문을 작성하는 것이 아닌, 하나로 모아서 처리할 수 있다.
* 각각의 기능이 명확하게 분리된다. 개발자가 어떤 기능을 수정해야 할 때, 수정해야 하는 클래스가 아주 명확해진다.

## 커맨드 패턴의 단점

* 복잡성 증가
  * Command 인터페이스와 구현체들, Command 객체를 호출하고 관리하는 클래스 등 여러 클래스를 생성해야 하기 때문에 코드의 복잡성이 증가할 수 있다.
* 트레이드 오프
  * 단순한 if문 몇개로 해결할 수 있는 문제에 복잡한 커맨드 패턴을 도입하는 것은 좋은 설계가 아닐 수 있다.
* 기능이 어느정도 있고, 각각의 기능이 명확하게 나누어질 수 있고, 향후 기능의 확장까지 고려해야 한다면 커맨드 패턴은 좋은 대안이 될 수 있다.

# 커맨트 패턴 예제

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

    public CommandManager(Playlist playlist) {
        commands.put("/add", new AddPlaylistCommand(playlist));
        commands.put("/remove", new RemovePlaylistCommand(playlist));
        commands.put("/read", new ReadPlaylistCommand(playlist));
        commands.put("/exit", new ExitPlaylistCommand());
    }

    public void execute(String input) {
        String[] args = input.split(DELIMITER);
        String key = args[0];

        Command command = commands.get(key);
        if (command == null) {
            System.out.println("잘못된 명령어입니다.");
            return;
        }
        command.execute(args);
    }
}

//Command Pattern
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