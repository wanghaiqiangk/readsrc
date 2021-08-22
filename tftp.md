# TFTP (Trivial File Transfer Protocol)

Repository: https://packages.ubuntu.com/bionic/tftp

## Ignore Function Parameters

I really didn't know how to ignore function parameters and how to suppress diagnostic message from compiler if `-Wextra -Wunused` is specified. So I'm curious about how QT does via `Q_UNUSED`. But the source code of tftp gives the following solution:

```c
void
setbinary(int argc, char *argv[])
{
	(void)argc;
	(void)argv;
	setmode("octet");
}
```

## Another Example of Socket

```c
void
setpeer(int argc, char *argv[])
{
	struct addrinfo hints, *aiptr, *ai;
    
    // ...

	memset(&hints, 0, sizeof(hints));
	hints.ai_flags = AI_ALL | AI_V4MAPPED | AI_ADDRCONFIG | AI_CANONNAME;
	hints.ai_family = AF_UNSPEC;
	hints.ai_socktype = SOCK_DGRAM;

	if ( getaddrinfo(argv[1], service, &hints, &aiptr) ) {
		connected = 0;
		printf("%s: unknown host\n", argv[1]);
		return;
	}

	/* Choose first applicable address. */
	ai = aiptr;

	while ( ai && (ai->ai_family != AF_INET6)
			&& (ai->ai_family != AF_INET) )
		ai = ai->ai_next;


	if ( ai == NULL ) {
		connected = 0;
		freeaddrinfo(aiptr);
		printf("%s: unknown host\n", argv[1]);
		return;
	}

	memcpy(&s_inn, ai->ai_addr, ai->ai_addrlen);
	s_inn_len = ai->ai_addrlen;
	connected = ai->ai_family;
	strncpy(hostname, aiptr->ai_canonname, sizeof(hostname));
	hostname[sizeof(hostname)-1] = '\0';
    printf("hostname: %s\n", hostname);
	freeaddrinfo(aiptr);

	/* Test and set socket for the relevant address family. */
	initsock(connected);
}
```

