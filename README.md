# Hyperledger-study-31

하이퍼레져 앱 만들기 실습 31일차 - pm2 설치 및 ui 표시

출처 : https://opentutorials.org/course/3332/21135

1. pm2 설치 - 사용의 편리성 증가시킴

        npm install pm2 -g - 설치

2. pm2 실행 - 사용중인 cpu확인

        start main.js

3. pm2 활용 - 코드 변경 시 새로고침으로 확인가능

        pm2 start main.js —watch

4. pm2 로그 확인

        pm2 log

5. hrml form 작성 - syntax/form.html

        <form action="http://localhost:3000/process_create"
        method="POST">
            <p>
                <input type="text" name="title">
            </p>
            <p>
                <textarea name="description"></textarea>
            </p>
            <p>
                <input type="submit">
            </p>
        </form>

6. ui 적용 - main.js

          var http = require('http');
          var fs = require('fs');
          var url = require('url');

          function templateHTML(title, list, body){
            return `
            <!doctype html>
            <html>
            <head>
              <title>WEB1 - ${title}</title>
              <meta charset="utf-8">
            </head>
            <body>
              <h1><a href="/">WEB</a></h1>
              ${list}
              <a href = "/create">create</a>
              ${body}
            </body>
            </html>
            `;
          }

          function templateList(filelist){
            var list = '<ul>';
            var i = 0;
            while(i < filelist.length){
              list = list + `<li><a href="/?id=${filelist[i]}">${filelist[i]}</a></li>`;
              i = i + 1;
            }
            list = list+'</ul>';
            return list;
          } 

          var app = http.createServer(function(request,response){
              var _url = request.url;
              var queryData = url.parse(_url, true).query;
              var pathname = url.parse(_url, true).pathname;
              if(pathname === '/'){
                if(queryData.id === undefined){
                  fs.readdir('./data', function(error, filelist){
                    var title = 'Welcome';
                    var description = 'Hello, Node.js';
                    var list = templateList(filelist);
                    var template = templateHTML(title, list, `<h2>${title}</h2>${description}`);
                    response.writeHead(200);
                    response.end(template);
                  })
                } else {
                  fs.readdir('./data', function(error, filelist){
                    fs.readFile(`data/${queryData.id}`, 'utf8', function(err, description){
                      var title = queryData.id;
                      var list = templateList(filelist);
                      var template = templateHTML(title, list, `<h2>${title}</h2>${description}`);
                      response.writeHead(200);
                      response.end(template);
                    });
                  });
                }
              } else if(pathname === '/create'){
                  fs.readdir('./data', function(error, filelist){
                    var title = 'WEB - create';
                    var list = templateList(filelist);
                    var template = templateHTML(title, list, `
                      <form action="http://localhost:3000/process_create"
                      method="POST">
                          <p>
                              <input type="text" name="title" placeholder="title">
                          </p>
                          <p>
                              <textarea name="description" placeholder="description"></textarea>
                          </p>
                          <p>
                              <input type="submit">
                          </p>
                      </form>
                    `);
                    response.writeHead(200);
                    response.end(template);
                  });
              } else {
                response.writeHead(404);
                response.end('Not found');
              }
          });
          app.listen(3000);
