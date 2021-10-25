```c
for (i=0; i<5; i++>){
    memset(&client, 0, sizeof(client));
    addrlen = sizeof(client);
    fds[i] = accept(sockfd, (struct sockaddr*)&client, &addrlen);
    if(fds[i] > max)
        max = fds[i];
}
while(1){
    FD_ZERO(&rset);
    for(i = 0; i<5; i++>){
        FD_SET(fds[i], &rset);
        //rset为一个bitmap，每一位表示哪个文件描述符是被启用的
    }
    
    select(max+1, &rset, NULL, NULL, NULL);
    //参数1，最大文件描述符+1
    //参数2，读文件描述符集合
    //参数3，写文件描述符集合
    //参数4，异常文件描述符集合
    //参数5，超时时间

    for(i=0; i<5; i++>){
        if(FD_ISSET(fds[i], &rset)){
            memset(buffer,0,MAXBUF);
            read(fds[i], buffer, MAXBUF);
            puts(buffer); 
        }


    }
}
```