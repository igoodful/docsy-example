---
title: encode-password
description: encode-password
date: 2023-10-24
weight: 70000


---

{{< alert >}}

1. 收费，易于学习

2. 适用MySQL

3. 轻量级数据库客户端软件，拥有图形化界面

{{< /alert >}}

### 版本


```python
if is_encode_password == True:
        password = base64.b64decode(password).decode("utf-8")

```



```c++

static inline bool is_base64(unsigned char c) { return (isalnum(c) || (c == '+') || (c == '/')); }
string base64_encode(unsigned char const *bytes_to_encode, unsigned int in_len) {
        string ret;
        int i = 0;
        int j = 0;
        unsigned char char_array_3[3];
        unsigned char char_array_4[4];

        while (in_len--) {
                char_array_3[i++] = *(bytes_to_encode++);
                if (i == 3) {
                        char_array_4[0] = (char_array_3[0] & 0xfc) >> 2;
                        char_array_4[1] = ((char_array_3[0] & 0x03) << 4) + ((char_array_3[1] & 0xf0) >> 4);
                        char_array_4[2] = ((char_array_3[1] & 0x0f) << 2) + ((char_array_3[2] & 0xc0) >> 6);
                        char_array_4[3] = char_array_3[2] & 0x3f;

                        for (i = 0; (i < 4); ++i) ret += base64_chars[char_array_4[i]];
                        i = 0;
                }
        }

        if (i) {
                for (j = i; j < 3; ++j) char_array_3[j] = '\0';

                char_array_4[0] = (char_array_3[0] & 0xfc) >> 2;
                char_array_4[1] = ((char_array_3[0] & 0x03) << 4) + ((char_array_3[1] & 0xf0) >> 4);
                char_array_4[2] = ((char_array_3[1] & 0x0f) << 2) + ((char_array_3[2] & 0xc0) >> 6);
                char_array_4[3] = char_array_3[2] & 0x3f;

                for (j = 0; j < i + 1; ++j) ret += base64_chars[char_array_4[j]];

                while (i++ < 3) ret += '=';
        }

        return ret;
}

string encode_plain_password(unsigned char const *bytes_to_encode, unsigned int in_len) {
        string ret = base64_encode(bytes_to_encode, in_len);
        return ret;
}

string decode_password(string const &encoded_string) {
        string ret = base64_decode(encoded_string);
        return ret;
}

string base64_decode(string const &encoded_string) {
        int in_len = encoded_string.size();
        int i = 0;
        int j = 0;
        int in_ = 0;
        unsigned char char_array_4[4], char_array_3[3];
        string ret;

        while (in_len-- && (encoded_string[in_] != '=') && is_base64(encoded_string[in_])) {
                char_array_4[i++] = encoded_string[in_];
                ++in_;
                if (i == 4) {
                        for (i = 0; i < 4; ++i) char_array_4[i] = base64_chars.find(char_array_4[i]);

                        char_array_3[0] = (char_array_4[0] << 2) + ((char_array_4[1] & 0x30) >> 4);
                        char_array_3[1] = ((char_array_4[1] & 0xf) << 4) + ((char_array_4[2] & 0x3c) >> 2);
                        char_array_3[2] = ((char_array_4[2] & 0x3) << 6) + char_array_4[3];

                        for (i = 0; (i < 3); ++i) ret += char_array_3[i];
                        i = 0;
                }
        }

        if (i) {
                for (j = i; j < 4; ++j) char_array_4[j] = 0;

                for (j = 0; j < 4; ++j) char_array_4[j] = base64_chars.find(char_array_4[j]);

                char_array_3[0] = (char_array_4[0] << 2) + ((char_array_4[1] & 0x30) >> 4);
                char_array_3[1] = ((char_array_4[1] & 0xf) << 4) + ((char_array_4[2] & 0x3c) >> 2);
                char_array_3[2] = ((char_array_4[2] & 0x3) << 6) + char_array_4[3];

                for (j = 0; (j < i - 1); ++j) ret += char_array_3[j];
        }

        return ret;
}
```




```c++
                        if (password && strlen(password) > 0) {
                                data_source_config_string.append("\npassword = ");
                                replace_password_with_instead_char(&password, "#", instead_char);
                                if (encode_password) {
                                        string encode_passwd =
                                            encode_plain_password((const unsigned char *)password, strlen(password));
                                        data_source_config_string.append(encode_passwd);
                                } else {
                                        data_source_config_string.append(password);
                                }
                        }
```
















