# EXPERIMENT 03 LINUX IPC - PIPES
## DEVELOPED BY : AKASH G
## REGISTER NO. : 212224100004
# AIM:
To write a C program that illustrate communication between two process using unnamed and named pipes
# DESIGN STEPS:
### Step 1:
Navigate to any Linux environment installed on the system or installed inside a virtual environment like virtual box/vmware or online linux JSLinux (https://bellard.org/jslinux/vm.html?url=alpine-x86.cfg&mem=192) or docker.

### Step 2:
Write the C Program using Linux Process API - pipe(), fifo()

### Step 3:
Testing the C Program for the desired output. 

# PROGRAM :
## C Program that illustrate communication between two process using unnamed pipes using Linux API system calls
```
#include <stdlib.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <string.h>
#include <fcntl.h>
#include <unistd.h>
#include <sys/wait.h>
#include <stdio.h>

void server(int, int);
void client(int, int);

int main() 
{ 
    int p1[2], p2[2], pid, status;

    if (pipe(p1) == -1 || pipe(p2) == -1) {
        perror("pipe");
        exit(1);
    }

    pid = fork();
    if (pid < 0) {
        perror("fork");
        exit(1);
    }

    if (pid == 0) {
        close(p1[1]);
        close(p2[0]);
        server(p1[0], p2[1]);
        return 0;
    }

    close(p1[0]);
    close(p2[1]);
    client(p1[1], p2[0]);
    wait(&status);
    return 0;
}

void server(int rfd, int wfd) 
{ 
    int n, fd;
    char fname[2000];
    char buff[2000];

    n = read(rfd, fname, sizeof(fname));
    if (n <= 0) {
        write(wfd, "Invalid file name\n", 19);
        return;
    }
    fname[n] = '\0';

    fd = open(fname, O_RDONLY);
    sleep(2);

    if (fd < 0) {
        write(wfd, "Can't open file\n", 16);
    } else {
        n = read(fd, buff, sizeof(buff));
        write(wfd, buff, n);
        close(fd);
    }
}

void client(int wfd, int rfd) 
{
    int n;
    char fname[2000];
    char buff[2000];

    printf("ENTER THE FILE NAME: ");
    scanf("%1999s", fname);

    printf("CLIENT SENDING THE REQUEST .... PLEASE WAIT\n");
    sleep(2);

    write(wfd, fname, strlen(fname));

    n = read(rfd, buff, sizeof(buff));
    buff[n] = '\0';

    printf("\nTHE RESULTS OF CLIENT ARE:\n");
    write(1, buff, n);
}
```

## OUTPUT:
![3A](https://github.com/user-attachments/assets/296ae3ae-11f2-4d28-bcae-738977571689)


## C Program that illustrate communication between two process using named pipes using Linux API system calls
```
#include <unistd.h>
#include <stdlib.h>
#include <stdio.h>
#include <sys/types.h>
#include <sys/stat.h>
int main(){
int res = mkfifo("/tmp/my_fifo", 0777);
if (res == 0) printf("FIFO created\n");
exit(EXIT_SUCCESS);
}
```
## OUTPUT:
![3B](https://github.com/user-attachments/assets/0aed6f2c-5a9a-4c5b-862e-29607bf10d43)
# RESULT :
The program is executed successfully.
