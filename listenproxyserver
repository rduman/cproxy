#include<stdio.h>
#include<stdlib.h>
#include<unistd.h>
#include<string.h>
#include<sys/types.h>
#include<sys/socket.h>
#include<arpa/inet.h>
#include<netinet/in.h>
#include<netdb.h>
#include<errno.h>


#define BUFFER_BOYUT 1000
#define BUFFER_REQUEST 250
int main(int argc,char* argv[]){
int soket,kabul,client_len;
pid_t pid;
struct sockaddr_in sunucu_addr,client_addr;
struct hostent *host;

if(argc<2){
	printf("ÖR: ./example <portno> şeklinde yazınız.\n");exit(EXIT_FAILURE);
}
printf("\nProxy Server a Hoşgeldiniz.\n");

bzero((char*)&sunucu_addr,sizeof(sunucu_addr));
bzero((char*)&client_addr, sizeof(client_addr));



sunucu_addr.sin_family=AF_INET;
sunucu_addr.sin_port=htons(atoi(argv[1]));
sunucu_addr.sin_addr.s_addr=INADDR_ANY;

soket=socket(AF_INET,SOCK_STREAM,IPPROTO_TCP);
if(soket<0){
	printf("Soket oluşturulamadı.\n"); exit(EXIT_FAILURE);
}
bind(soket,(struct sockaddr *)&sunucu_addr,sizeof(sunucu_addr));
if(soket<0){
	printf("Soket bağlantısı başarısız.\n");exit(EXIT_FAILURE);
}

listen(soket,100);
client_len=sizeof(client_addr);

KabulEt:

kabul=accept(soket,(struct sockaddr *)&client_addr,&client_len);
if(soket<0){
	printf("Kabul başarısız.\n"); exit(EXIT_FAILURE);
}
pid=fork();

if(pid==0){
	struct sockaddr_in host_addr;
	int bayrak=0,kabul1,n,port=0,i,soket1;
	char buffer[BUFFER_BOYUT],h1[BUFFER_REQUEST],h2[BUFFER_REQUEST],h3[BUFFER_REQUEST];
	char* temp=NULL;
bzero((char*)buffer,BUFFER_BOYUT);
recv(kabul,buffer,BUFFER_BOYUT,0);

sscanf(buffer,"%s %s %s",h1,h2,h3);

if(((strncmp(h1,"GET",3)==0))&&((strncmp(h3,"HTTP/1.1",8)==0)||(strncmp(h3,"HTTP/1.0",8)==0))&&(strncmp(h2,"http://",7)==0))
{
strcpy(h1,h2);

	for(i=7;i<strlen(h2);i++){
		if(h2[i]==':'){
			bayrak=1;
			break;
		}
	}

temp=strtok(h2,"//");
	if(bayrak==0){
		port=80;
		temp=strtok(NULL,"/");
	}
	else{
		temp=strtok(NULL,":");
	}
sprintf(h2,"%s",temp);
printf("host = %s",h2);
host=gethostbyname(h2);

	if(bayrak==1){
		temp=strtok(NULL,"/");
		port=atoi(temp);
	}

	strcat(h1,"^]");
	temp=strtok(h1,"//");
	temp=strtok(NULL,"/");
	if(temp!=NULL)
		temp=strtok(NULL,"^]");
	printf("\npath = %s\nPort = %d\n",temp,port);



bzero((char*)&host_addr,sizeof(host_addr));
host_addr.sin_port=htons(port);
host_addr.sin_family=AF_INET;
bcopy((char*)host->h_addr,(char*)&host_addr.sin_addr.s_addr,host->h_length);

soket1=socket(AF_INET,SOCK_STREAM,IPPROTO_TCP);
kabul1=connect(soket1,(struct sockaddr*)&host_addr,sizeof(struct sockaddr));
sprintf(buffer,"\n %s Sitesine bağlandı. IP: - %s\n",h2,inet_ntoa(host_addr.sin_addr));

if(kabul1<0){
	printf("Remote Server bağlantısında hata oluştu.\n");
	exit(EXIT_FAILURE);
}

printf("\n%s\n",buffer);
bzero((char*)buffer,sizeof(buffer));
if(temp!=NULL)
	sprintf(buffer,"GET /%s %s\r\nHost: %s\r\nConnection: close\r\n\r\n",temp,h3,h2);
else
	sprintf(buffer,"GET / %s\r\nHost: %s\r\nConnection: close\r\n\r\n",h3,h2);

n=send(soket1,buffer,strlen(buffer),0);
printf("\n%s\n",buffer);
if(n<0)
	printf("Hata sokete yazılıyor.\n ");
else{
	do{
	   bzero((char*)buffer,500);
	   n=recv(soket1,buffer,500,0);
	   	if(!(n<=0))
	   		send(kabul,buffer,n,0);
	  }while(n>0);
    }
}
else
{
send(kabul,"400 : BAD REQUEST\nONLY HTTP REQUESTS ALLOWED",18,0);
}
close(soket1);
close(kabul);
close(soket);
_exit(0);
}
else
{
close(kabul);
goto KabulEt;
}
return 0;
}
