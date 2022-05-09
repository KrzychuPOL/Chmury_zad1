
# Zadanie 1

## 1.

Screen:
![7](./screeny/7.png)


##  2.

dockerfile
```sh
# syntax=docker/dockerfile:1

# Autor Krzysztof Zarębski

FROM node:latest

WORKDIR /app
COPY index.js package.json package-lock.json ./
RUN npm install --production

ENTRYPOINT [ "node" ]
CMD [ "index.js" ]

```

##  3.
a. Budowa obrazu:

```sh
docker build -t technologie_chmurowe_zadanie_1 .
```
![10](./screeny/10.png)
b. Utworzenie kontenera i podanie portu

```sh
docker create -p 8080:80 --name tc_zadanie1_kontener technologie_chmurowe_zadanie_1
```
![11](./screeny/11.png)

c. Trzeba użyć polecenia `docker logs` 

![14](./screeny/14.png)

Jako alternatywa można skorzystać z desktopowej wersji aplikacji Docker i tam wyświetlić interesujące nas logi:

![12](./screeny/12.png)

![13](./screeny/13.png)

d. Należy użyć polecenia `docker history`:

![15](./screeny/15.png)

## 4. (Dodatek 1)
**Plik dodatek1.yml w katalogu .github/workflows/**

Budowa obrazów po publikacji branch master repo:
```sh
on:
  push:
    branches: [master]
```
Architektury wybiera się za pomocą parametru platforms:
```sh
platforms: linux/arm/v7, linux/arm64/v8, linux/amd64

```
Do zbudowania obrazu należy spushować branch na repozytorium poprzez polecenie:
```sh
git push --force github HEAD:master
```
Push na GitHub Actions:
![16](./screeny/16.png)
## GitHub Container Registry
Do opublikowania image na GitHub Container Registry należy podać adres rejestru do logowania oraz prefiks do nazwy tagu w kroku budowania. By nie duplikować adresu repozytorium oraz nazwy tagu, zapisać je trzeba w zmiennych środowiskowych.
```sh
env:
registry: ghcr.io
IMAGE_NAME: technologie_chmurowe_zadanie_1:latest

```
Należy wykorzystać te zmienne jako parametry do logowania do repozytorium oraz budowania i publikacji. Do parametru tags trzeba podać 2 tagi. 1 odwołujący się do DockerHub i 2 do GitHub Container Registry.
```sh

      with:
          registry: ghcr.io
          username: ${{ github.actor }}
          password: ${{ secrets.TOKEN }}

          tags: |
            ghcr.io/${{ env.IMAGE_REPOSITORY }},${{ secrets.DOCKER_HUB_USERNAME }}/${{ env.IMAGE_NAME }}
```
## Cache
GitHub Actions do przechowywania cache: Należy ustawić parametry cache-from i cache-to na wartość **gha** co oznacza integrację Dockera z GitHub Actions.
```sh
          cache-from: type=gha
          cache-to: type=gha
```
Do sprawdzenia działania cache trzeba skorzystać z polecenia:
```sh
OWNER='KrzychuPOL' REPO='Chmury_zad1' sh -c 'curl "https://api.github.com/repos/${OWNER}/${REPO}/actions/cache/usage"'
```
![17](./screeny/17.png)

## Dodatek 2
## 1 a)
Należy pobrać obraz rejestru:
![18](./screeny/18.png)
Utworzenie kontenera. Listning ustawiony na port 5000, przekierujemy komunikację z portu 6677 na port 5000 z użyciem parametru -p

![19](./screeny/19.png)
![20](./screeny/20.png)

Uruchomienie kontenera:

![21](./screeny/21.png)

Serwer rejestru działa.
### 1 b)
Pobranie obrazu ubuntu:

![22](./screeny/22.png)

Zmiana nazwy, zapis w rejestrze:

![23](./screeny/23.png)

## 2.
Do włączenia mechanizmu basic auth potrzeba włączyć TLS. Należy skorzystać z protokołu HTTPS i certyfikatu. Wygenerowałem cały certyfikat i dodałem go do magazynu systemu operacyjnego.

![24](./screeny/24.png)
![25](./screeny/25.png)

Dodanie certyfikatu do systemu:

![26](./screeny/26.png)
![27](./screeny/27.png)

Należy zawsze ufać certyfikatowi.

Generowanie hasha hasła dla użytkownika w pliku htpasswd

![28](./screeny/28.png)

Ponowne utworzenie kontenera z nową konfiguracją i plikiem httpasswd z użyciem bind-mount

![29](./screeny/29.png)
![30](./screeny/30.png)

Działanie basic auth:

![31](./screeny/31.png)
![32](./screeny/32.png)
