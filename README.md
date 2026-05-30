# Laboratorium 12 — Docker networking and volumes

## Autor

**Imię i nazwisko:** Oleksandr Pyrluk
**Przedmiot:** Programowanie Aplikacji w Chmurze Obliczeniowej
**Laboratorium:** 12

---

## Cel zadania

Celem laboratorium było uruchomienie trzech kontenerów Docker z serwerem **nginx:latest** w taki sposób, aby:

* kontenery `web1`, `web2`, `web3` były podłączone do jednej sieci mostkowej `lab12net`,
* każdy serwer był dostępny z poziomu komputera hosta,
* każdy serwer wyświetlał prostą stronę HTML,
* strona HTML była zamontowana do kontenerów jako wolumen z uprawnieniami `read-only`,
* logi każdego serwera były zapisywane w osobnych katalogach na komputerze hosta.

---

## Struktura projektu

```text
Lab12/
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

## Utworzenie sieci Docker

```powershell
docker network create lab12net
```

Sprawdzenie sieci:

```powershell
docker network ls
```

---

## Uruchomienie kontenerów

### Kontener web1

```powershell
docker run -d --name web1 --network lab12net -p 8081:80 -v "D:\VS_Code\Lab12\html:/usr/share/nginx/html:ro" -v "D:\VS_Code\Lab12\logs\web1:/var/log/nginx" nginx:latest
```

### Kontener web2

```powershell
docker run -d --name web2 --network lab12net -p 8082:80 -v "D:\VS_Code\Lab12\html:/usr/share/nginx/html:ro" -v "D:\VS_Code\Lab12\logs\web2:/var/log/nginx" nginx:latest
```

### Kontener web3

```powershell
docker run -d --name web3 --network lab12net -p 8083:80 -v "D:\VS_Code\Lab12\html:/usr/share/nginx/html:ro" -v "D:\VS_Code\Lab12\logs\web3:/var/log/nginx" nginx:latest
```

---

## Sprawdzenie działania kontenerów

```powershell
docker ps
```

Kontenery działają na portach:

```text
web1 -> http://localhost:8081
web2 -> http://localhost:8082
web3 -> http://localhost:8083
```

---

## Sprawdzenie dostępności stron

```powershell
curl http://localhost:8081
curl http://localhost:8082
curl http://localhost:8083
```

Każdy serwer zwraca kod:

```text
StatusCode : 200
StatusDescription : OK
```

---

## Sprawdzenie sieci lab12net

```powershell
docker network inspect lab12net
```

W wyniku polecenia widoczne są trzy kontenery:

```text
web1 -> 172.20.0.2
web2 -> 172.20.0.3
web3 -> 172.20.0.4
```

---

## Sprawdzenie wolumenu read-only

```powershell
docker inspect web1 | findstr RW
```

Wynik:

```text
"RW": false
```

Oznacza to, że katalog ze stroną HTML został zamontowany jako `read-only`.

---

## Sprawdzenie logów

```powershell
dir D:\VS_Code\Lab12\logs\web1
dir D:\VS_Code\Lab12\logs\web2
dir D:\VS_Code\Lab12\logs\web3
```

W katalogach znajdują się pliki:

```text
access.log
error.log
```

Sprawdzenie zawartości logów:

```powershell
type D:\VS_Code\Lab12\logs\web1\access.log
type D:\VS_Code\Lab12\logs\web2\access.log
type D:\VS_Code\Lab12\logs\web3\access.log
```

W plikach `access.log` widoczne są zapytania HTTP, np.:

```text
GET / HTTP/1.1 200
```

---

## Usunięcie kontenerów i sieci

```powershell
docker stop web1 web2 web3
docker rm web1 web2 web3
docker network rm lab12net
```

---

## Wnioski

W ramach laboratorium utworzono własną sieć mostkową Docker `lab12net` oraz uruchomiono trzy kontenery nginx. Każdy kontener został udostępniony na osobnym porcie hosta. Strona HTML została zamontowana do kontenerów jako wolumen tylko do odczytu, a logi każdego serwera są zapisywane w osobnych katalogach na komputerze hosta.
