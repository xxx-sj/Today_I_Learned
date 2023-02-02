# optimizing for throughput - HTTP server + Jmeter

```java
/*
 * MIT License
 *
 * Copyright (c) 2019 Michael Pogrebinsky
 *
 * Permission is hereby granted, free of charge, to any person obtaining a copy
 * of this software and associated documentation files (the "Software"), to deal
 * in the Software without restriction, including without limitation the rights
 * to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
 * copies of the Software, and to permit persons to whom the Software is
 * furnished to do so, subject to the following conditions:
 *
 * The above copyright notice and this permission notice shall be included in all
 * copies or substantial portions of the Software.
 *
 * THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
 * IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
 * FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
 * AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
 * LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
 * OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
 * SOFTWARE.
 */

import com.sun.net.httpserver.HttpExchange;
import com.sun.net.httpserver.HttpHandler;
import com.sun.net.httpserver.HttpServer;

import java.io.IOException;
import java.io.OutputStream;
import java.net.InetSocketAddress;
import java.nio.file.Files;
import java.nio.file.Paths;
import java.util.concurrent.Executor;
import java.util.concurrent.Executors;

/**
 * Optimizing for Throughput Part 2 - HTTP server + Jmeter
 * https://www.udemy.com/java-multithreading-concurrency-performance-optimization
 */
public class ThroughputHttpServer {
    private static final String INPUT_FILE = "./resources/war_and_peace.txt";
    private static final int NUMBER_OF_THREADS = 8;

    public static void main(String[] args) throws IOException {
        //readALlBytes로 path에 해당하는 글을 String으로 읽어들인다.
        String text = new String(Files.readAllBytes(Paths.get(INPUT_FILE)));
        //start server method에 전달한다.
        startServer(text);
    }

    public static void startServer(String text) throws IOException {
        //http server를 만든다.
        //listen 하고 싶은 host와 port를 지정한다. [localhost:8080]
        //create의 2번 째 인자로는 backlog size이다.
        //http server request에 대한 queue size이다.
        //소켓 백로그. 이 값이 0보다 작거나 같으면 시스템 기본값이 사용됩니다.
        //0으로 한 이유는 모든 request는 thread pulse queue에서 끝나야 하기 때문이다.
        HttpServer server = HttpServer.create(new InetSocketAddress(8000), 0);
        //http server object를 성공적으로 생성했다면 기본적으로 핸들러 객체를 할당하는
        //context를 만들어야 한다.
        server.createContext("/search", new WordCountHandler(text));
        //각각 들어오는 http에 대해 schedule 할 pool worker thread를 만든다.
        Executor executor = Executors.newFixedThreadPool(NUMBER_OF_THREADS);
        server.setExecutor(executor);
        server.start();
    }

    //각각의 http 요청에 대한 handler 이다.
    private static class WordCountHandler implements HttpHandler {
        private String text;

        //생성자로 text를 받고 있으며.
        public WordCountHandler(String text) {
            this.text = text;
        }

        // override를 통해 handle을 정의한다.
        @Override
        public void handle(HttpExchange httpExchange) throws IOException {
            //넘겨 받은 quert String에서
            String query = httpExchange.getRequestURI().getQuery();
            //split을 통해 string을 분리하여
            String[] keyValue = query.split("=");
            //첫 번째로 받은 query String은 name으로 action에 해당하고
            String action = keyValue[0];
            // 두 번째로 받은 query String은 value로 word에 해당한다.
            String word = keyValue[1];
            //action[ name ]이 word와 같지 않다면
            if (!action.equals("word")) {
                //400 에러를 내려보낸다.
                httpExchange.sendResponseHeaders(400, 0);
                return;
            }
            //queryString의 value에 해당하는 값을 countWord method 인자로 넘긴다.
            long count = countWord(word);

            //바이너리로 직렬화해서
            byte[] response = Long.toString(count).getBytes();
            //http 헤더로는 200 ok 를 내려준다.
            httpExchange.sendResponseHeaders(200, response.length);
            //response body에 write로 response를 적고 내려준다. 끝
            OutputStream outputStream = httpExchange.getResponseBody();
            outputStream.write(response);
            outputStream.close();
        }
        //찾을 word를 넘겨받아서
        private long countWord(String word) {
            long count = 0;
            int index = 0;
            //index가 0보다 클 때까지 반복하면서 찾는다.
            while (index >= 0) {
                //indexof를 통해 해당 index부터 찾으려 하는 word가 있는지 검색한다.
                index = text.indexOf(word, index);
                //있다면 index 값을 없다면 -1를 return 한다.

                //있다면, count와 index를 증가시킨다.
                //index를 증가시키는 이유는 찾은 index값 다음 값부터 시작하기 위해 index를 증가시킨다.
                if (index >= 0) {
                    count++;
                    index++;
                }
            }
            return count;
        }
    }
}
```

이후 main을 실행하여 서버를 띄운 후 브라우저에서 [ localhost:8000/search?word=(word which want to found) ] 하면 화면에 count 값이 나오게 된다.


### Jmeter 사용하기
- java performance automation tool
- does not require writing any code
요청을 여러번 보내어 requrest / test duration 을 통해 throughput [ 처리량 ]을 나타낸다.

jmeter로 테스트를 진행해보면 물리적 코어수 만큼 thread를 추가하면 req/sec는 증가하게 가파르게 증가하게 된다.
물리적 코어 에서 가상코어 까지의 thread 증가에서는 완만하게 증가하다가 가상코어를 넘어가면 더이상 증가폭이 보이지 않게 된다.

##### 출처: https://www.udemy.com/course/java-multithreading-concurrency-performance-optimization/learn/lecture/11199874#content
