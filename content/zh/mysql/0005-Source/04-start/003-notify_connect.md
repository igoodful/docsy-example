---
title: 03. notify_connect
description: systemd-notify
date: 2023-10-30
weight: 30


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}
8.0.32
{{< /alert >}}

{{<alert>}}


{{</alert>}}


```c++
void notify_connect() {
#ifndef _WIN32
        const char *sockstr = getenv("NOTIFY_SOCKET");
        if (sockstr == nullptr) {
#ifdef WITH_SYSTEMD_DEBUG // 一般没有定义
                LogErr(WARNING_LEVEL, ER_SYSTEMD_NOTIFY_DEBUG,
                       "NOTIFY_SOCKET not set in environment. sd_notify messages "
                       "will not be sent!",
                       "");
#endif /* WITH_SYSTEMD_DEBUG */
                return; // 通常在这里就结束了
        }
        size_t sockstrlen = strlen(sockstr);
        size_t sunpathlen = sizeof(sockaddr_un::sun_path) - 1;
        if (sockstrlen > sunpathlen) {
                LogErr(SYSTEM_LEVEL, ER_SYSTEMD_NOTIFY_PATH_TOO_LONG, sockstr, sockstrlen, sunpathlen);
                return;
        }
        NotifyGlobals::socket = socket(AF_UNIX, SOCK_DGRAM, 0);

        sockaddr_un addr;
        socklen_t addrlen;
        memset(&addr, 0, sizeof(sockaddr_un));
        addr.sun_family = AF_UNIX;
        if (sockstr[0] != '@') {
                strcpy(addr.sun_path, sockstr);
                addrlen = offsetof(struct sockaddr_un, sun_path) + sockstrlen + 1;
        } else {  // Abstract namespace socket
                addr.sun_path[0] = '\0';
                strncpy(&addr.sun_path[1], sockstr + 1, strlen(sockstr) - 1);
                addrlen = offsetof(struct sockaddr_un, sun_path) + sockstrlen;
        }
        int ret = -1;
        do {
                ret = connect(NotifyGlobals::socket, reinterpret_cast<const sockaddr *>(&addr), addrlen);
        } while (ret == -1 && errno == EINTR);
        if (ret == -1) {
                char errbuf[512];
                LogErr(WARNING_LEVEL, ER_SYSTEMD_NOTIFY_CONNECT_FAILED, sockstr,
                       my_strerror(errbuf, sizeof(errbuf) - 1, errno));
                NotifyGlobals::socket = -1;
        }
#endif /* not defined _WIN32 */
}
```












