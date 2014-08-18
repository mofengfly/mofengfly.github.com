---
layout: post
title: nodejs mysql Error: Connection lost The server closed the connection
categories: nodejs
---

做的一个nodejs小应用部署之后，发现经常出现'nodejs mysql Error: Connection lost The server closed the connection'，由于没有捕获错误，导致服务经常重启。原来是因为MySQL中有一个名叫wait_timeout的变量，表示操作超时时间，当连接超过一定时间没有活动后，会自动关闭该连接，这个值默认为28800（即8小时）。在stackoverflow上找到一种解决方案：

    var db_config = {
      host: 'localhost',
        user: 'root',
        password: '',
        database: 'example'
    };

    var connection;

    function handleDisconnect() {
      connection = mysql.createConnection(db_config); // Recreate the connection, since
                                                      // the old one cannot be reused.

      connection.connect(function(err) {              // The server is either down
        if(err) {                                     // or restarting (takes a while sometimes).
          console.log('error when connecting to db:', err);
          setTimeout(handleDisconnect, 2000); // We introduce a delay before attempting to reconnect,
        }                                     // to avoid a hot loop, and to allow our node script to
      });                                     // process asynchronous requests in the meantime.
                                              // If you're also serving http, display a 503 error.
      connection.on('error', function(err) {
        console.log('db error', err);
        if(err.code === 'PROTOCOL_CONNECTION_LOST') { // Connection to the MySQL server is usually
          handleDisconnect();                         // lost due to either server restart, or a
        } else {                                      // connnection idle timeout (the wait_timeout
          throw err;                                  // server variable configures this)
        }
        
        connection = mysql.createConnection(db_config);
        
        
参考：

http://cnodejs.org/topic/516b77e86d382773064266df

http://stackoverflow.com/questions/20210522/nodejs-mysql-error-connection-lost-the-server-closed-the-connection


        
        
  });
}

handleDisconnect();