https://kasyalog.site/blog/spring-security-api-auth/

https://kotapontan.hatenablog.com/entry/2020/06/10/RequestBody%E3%81%AE%E5%86%85%E5%AE%B9%E3%81%AB%E3%82%88%E3%81%A3%E3%81%A6%E4%BD%BF%E7%94%A8%E3%81%99%E3%82%8B%E3%83%AA%E3%82%BD%E3%83%BC%E3%82%B9%E3%82%92%E4%BD%BF%E3%81%84%E5%88%86%E3%81%91

https://qiita.com/syukai/items/f03844feca78572ce24f

# public_tmp

curl -v -b cookie.txt -c cookie.txt "http://localhost:8080/session/set?name=foo&bloodType=a"
curl -v -b cookie.txt -c cookie.txt "http://localhost:8080/session/get"
curl -v -b cookie.txt -c cookie.txt "http://localhost:8080/session/set?name=bar&bloodType=b"
curl -v -b cookie.txt -c cookie.txt "http://localhost:8080/session/get"
curl -v -b cookie.txt -c cookie.txt "http://localhost:8080/session/delete"
curl -v -b cookie.txt -c cookie.txt "http://localhost:8080/session/get"


SessionController.java
```Java
package dev.itboot.rest.controller;

import jakarta.servlet.http.HttpSession;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.ResponseBody;

@Controller
@RequestMapping("/session")
public class SessionController {
    @GetMapping("/set")
    @ResponseBody
    public String set(@RequestParam("name") String name, @RequestParam("bloodType") String bloodType, HttpSession session) {
        session.setAttribute("name", name);
        session.setAttribute("bloodType", bloodType);
        return "保存しました";
    }

    @GetMapping("/get")
    @ResponseBody
    public String get(HttpSession session) {
        String name = (String) session.getAttribute("name");
        String bloodType = (String) session.getAttribute("bloodType");
        if (name == null) {
            name = "名無し";
        }
        if (bloodType == null) {
            bloodType = "不明";
        }
        return "名前: " + name + "<br>血液型: " + bloodType;
    }

    @GetMapping("/delete")
    @ResponseBody
    public String delete(HttpSession session) {
        session.removeAttribute("name");
        session.removeAttribute("bloodType");
        return "削除しました";
    }
}
```

curl -X POST -H "Content-Type: application/json" -d "{\"processName\":\"keyvalue\", \"name\":\"taro\", \"age\":\"30\", \"note\":\"hoge\"}" http://localhost:8080/api/general -v

SampleController.java
```Java
package dev.itboot.rest.controller;

import dev.itboot.rest.response.SampleMessageResponse;
import dev.itboot.rest.response.SampleKeyValueResponse;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
import java.util.Map;

@RestController
@RequestMapping("/api")
public class SampleController {
    @PostMapping("/general")
    private ResponseEntity<Object> general(@RequestBody Map<String, String> body) {
        String processName = body.get("processName");
        return switch (processName) {
            case "message" -> new ResponseEntity<>(new SampleMessageResponse(1, "hello1", "note1"), HttpStatus.OK);
            case "keyvalue" -> new ResponseEntity<>(new SampleKeyValueResponse("value222", "key222"), HttpStatus.OK);
            default -> throw new IllegalArgumentException("Invalid processName: " + processName);
        };
    }
}
```

SampleKeyValueResponse.java
※並び替えのアノテーションあり
```Java
package dev.itboot.rest.response;

import com.fasterxml.jackson.annotation.JsonPropertyOrder;

@JsonPropertyOrder({"key", "value"})
public class SampleKeyValueResponse {
    public String value;
    public String key;

    public SampleKeyValueResponse(String value, String key) {
        this.value = value;
        this.key = key;
    }
}
```

SampleMessageResponse.java
```Java
package dev.itboot.rest.response;

public class SampleMessageResponse {
    public int id;
    public String message;
    public String note;

    public SampleMessageResponse(int id, String message, String note) {
        this.id = id;
        this.message = message;
        this.note = note;
    }
}
```

