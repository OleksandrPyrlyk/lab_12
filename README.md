# Laboratorium 12 – Docker Networking and Volumes

## Student

**Imię i nazwisko:** Oleksandr Pyrluk
**Przedmiot:** Programowanie Aplikacji w Chmurze Obliczeniowej
**Laboratorium:** 12

---

# Cel laboratorium

Celem laboratorium było:

* utworzenie własnej sieci Docker typu bridge,
* uruchomienie trzech kontenerów nginx,
* udostępnienie serwerów na różnych portach hosta,
* wykorzystanie wolumenów Docker,
* zastosowanie wolumenów typu read-only,
* zapis logów nginx do katalogów hosta.

---

# Struktura projektu

```text
Lab12/
│
├── README.md
├── html/
│   └── index.html
├── logs/
│   ├── web1/
│   ├── web2/
│   └── web3/
└── screenshots/
```

---

# Utworzenie sieci Docker

Polecenie:

```powershell
docker network create lab12net
```

Sprawdzenie sieci:

```powershell
docker network ls
```

### Screenshot

<img width="399" height="137" alt="image" src="https://github.com/user-attachments/assets/34157cc5-5cc4-4d2b-b650-80cf2e058e09" />


---

# Uruchomienie kontenerów

## web1

```powershell
docker run -d --name web1 --network lab12net -p 8081:80 -v "D:\VS_Code\Lab12\html:/usr/share/nginx/html:ro" -v "D:\VS_Code\Lab12\logs\web1:/var/log/nginx" nginx:latest
```

## web2

```powershell
docker run -d --name web2 --network lab12net -p 8082:80 -v "D:\VS_Code\Lab12\html:/usr/share/nginx/html:ro" -v "D:\VS_Code\Lab12\logs\web2:/var/log/nginx" nginx:latest
```

## web3

```powershell
docker run -d --name web3 --network lab12net -p 8083:80 -v "D:\VS_Code\Lab12\html:/usr/share/nginx/html:ro" -v "D:\VS_Code\Lab12\logs\web3:/var/log/nginx" nginx:latest
```

---

# Lista uruchomionych kontenerów

Polecenie:

```powershell
docker ps
```

### Screenshot

![Docker PS](screenshots/docker_ps.png)

---

# Dostępność serwerów

Adresy:

```text
http://localhost:8081
http://localhost:8082
http://localhost:8083
```

### Screenshot web1

![Web1](screenshots/web1_page.png)

### Screenshot web2

![Web2](screenshots/web2_page.png)

### Screenshot web3

![Web3](screenshots/web3_page.png)

---

# Test przy użyciu CURL

```powershell
curl http://localhost:8081
curl http://localhost:8082
curl http://localhost:8083
```

### Screenshot

![Curl Test](screenshots/curl_test.png)

---

# Sprawdzenie sieci lab12net

Polecenie:

```powershell
docker network inspect lab12net
```

Wynik potwierdza obecność:

* web1
* web2
* web3

### Screenshot

![Network Inspect](screenshots/network_inspect.png)

---

# Sprawdzenie wolumenu Read Only

Polecenie:

```powershell
docker inspect web1 | findstr RW
```

Wynik:

```text
"RW": false
```

### Screenshot

![Read Only Volume](screenshots/read_only_volume.png)

---

# Logi nginx

Sprawdzenie katalogów:

```powershell
dir D:\VS_Code\Lab12\logs\web1
dir D:\VS_Code\Lab12\logs\web2
dir D:\VS_Code\Lab12\logs\web3
```

### Screenshot

![Logs Directory](screenshots/logs_directory.png)

---

# Zawartość logów

Polecenia:

```powershell
type D:\VS_Code\Lab12\logs\web1\access.log
type D:\VS_Code\Lab12\logs\web2\access.log
type D:\VS_Code\Lab12\logs\web3\access.log
```

Przykładowy wpis:

```text
GET / HTTP/1.1 200
```

### Screenshot

![Access Logs](screenshots/access_logs.png)

---

# Kod strony HTML

Plik:

```html
<!DOCTYPE html>
<html lang="pl">
<head>
    <meta charset="UTF-8">
    <title>Laboratorium 12</title>
</head>
<body>
    <h1>Laboratorium 12</h1>
    <p>Imię i nazwisko: Oleksandr Pyrluk</p>
    <p>Serwer nginx działa poprawnie.</p>
</body>
</html>
```

---

# Wnioski

W ramach laboratorium utworzono własną sieć Docker typu bridge o nazwie **lab12net**. Uruchomiono trzy kontenery nginx (**web1**, **web2**, **web3**) dostępne na różnych portach hosta. Strona HTML została zamontowana jako wolumen typu **read-only**, a logi każdego kontenera są przechowywane w oddzielnych katalogach na komputerze hosta. Testy potwierdziły poprawne działanie wszystkich serwerów oraz prawidłowe zapisywanie logów.
