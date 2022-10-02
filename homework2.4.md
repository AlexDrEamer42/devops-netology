1. Найдите полный хеш и комментарий коммита, хеш которого начинается на `aefea`.

```
git log --pretty=oneline -n 1 aefea
```

```
aefead2207ef7e2aa5dc81a34aedf0cad4c32545 Update CHANGELOG.md
```

2. Какому тегу соответствует коммит `85024d3`?


```
git tag --points-at 85024d3
```

```
v0.12.23
```


3. Сколько родителей у коммита `b8d720`? Напишите их хеши.


```
git log --pretty=%P -n 1 b8d720
```

```
56cd7859e05c36c06b56d013b55a252d0bb7e158 9ea88f22fc6269854151c571162c5bcf958bee2b
```

У коммита 2 родителя.

4. Перечислите хеши и комментарии всех коммитов которые были сделаны между тегами v0.12.23 и v0.12.24.

```
git log --pretty=oneline v0.12.23...v0.12.24
```

```
33ff1c03bb960b332be3af2e333462dde88b279e (tag: v0.12.24) v0.12.24
b14b74c4939dcab573326f4e3ee2a62e23e12f89 [Website] vmc provider links
3f235065b9347a758efadc92295b540ee0a5e26e Update CHANGELOG.md
6ae64e247b332925b872447e9ce869657281c2bf registry: Fix panic when server is unreachable
5c619ca1baf2e21a155fcdb4c264cc9e24a2a353 website: Remove links to the getting started guide's old location
06275647e2b53d97d4f0a19a0fec11f6d69820b5 Update CHANGELOG.md
d5f9411f5108260320064349b757f55c09bc4b80 command: Fix bug when using terraform login on Windows
4b6d06cc5dcb78af637bbb19c198faff37a066ed Update CHANGELOG.md
dd01a35078f040ca984cdd349f18d0b67e486c35 Update CHANGELOG.md
225466bc3e5f35baa5d07197bbc079345b77525e Cleanup after v0.12.23 release
```

5. Найдите коммит, в котором была создана функция `func providerSource`, ее определение в коде выглядит так `func providerSource(...)` (вместо троеточия перечислены аргументы).


```
git log -S "func providerSource" --oneline
```

```
5af1e6234a main: Honor explicit provider_installation CLI config when present
8c928e8358 main: Consult local directories as potential mirrors of providers
```

Проверяем более старый коммит.


```
git show 8c928e8358 | grep "func providerSource"
```


```
+func providerSource(services *disco.Disco) getproviders.Source {
```

Функция создана в коммите 8c928e8358.


6. Найдите все коммиты, в которых была изменена функция `globalPluginDirs`. 


```
git log -S "globalPluginDirs" --pretty=oneline
```

```
125eb51dc40b049b38bf2ed11c32c6f594c8ef96 Remove accidentally-committed binary
22c121df8631c4499d070329c9aa7f5b291494e1 Bump compatibility version to 1.3.0 for terraform core release (#30988)
35a058fb3ddfae9cfee0b3893822c9a95b920f4c main: configure credentials from the CLI config file
c0b17610965450a89598da491ce9b6b5cbd6393f prevent log output during init
8364383c359a6b738a436d1b7745ccdce178df47 Push plugin discovery down into command package
```

7. Кто автор функции `synchronizedWriters`?

```
git log -S "synchronizedWriters" --pretty=short
```

```
commit bdfea50cc85161dea41be0fe3381fd98731ff786
Author: James Bardin <j.bardin@gmail.com>

    remove unused

commit fd4f7eb0b935e5a838810564fd549afe710ae19a
Author: James Bardin <j.bardin@gmail.com>

    remove prefixed io

commit 5ac311e2a91e381e2f52234668b49ba670aa0fe5
Author: Martin Atkins <mart@degeneration.co.uk>

    main: synchronize writes to VT100-faker on Windows
```

Проверяем более старый коммит.


```
git show 5ac311e2a91e381e2f52234668b49ba670aa0fe5 | grep synchronizedWriters
```

```
+		wrapped := synchronizedWriters(stdout, stderr)
+// synchronizedWriters takes a set of writers and returns wrappers that ensure
+func synchronizedWriters(targets ...io.Writer) []io.Writer {
```

Автор функции - Martin Atkins







