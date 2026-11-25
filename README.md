# F.X. CarWash — nový web (statická verze)

Nahrazuje současnou React aplikaci na `www.fxcarwash.cz`.

---

## Co to je

Čistě statický web — žádný build, žádné závislosti, žádné proměnné
prostředí. Pět stránek, obrázky a fonty. Nic nevolá žádné API.

**Tato verze neobsahuje zákaznické účty ani administraci.**
Přihlášení, registrace a správa karet budou v následující verzi.
Backend na `api.fxcarwash.cz` (Railway) běží dál beze změny — tento
web ho jen zatím nevolá.

---

## Nasazení (Vercel)

Nasazuje se **obsah této složky**, ne složka samotná.

**Přes Vercel CLI:**

```bash
cd fxcarwash-web
npx vercel          # náhled, vypíše preview URL
npx vercel --prod   # ostrá verze
```

**Přes Git:** nahrát obsah do repozitáře projektu. Pokud soubory nejsou
v kořeni repozitáře, nastavit ve Vercelu **Root Directory** na složku,
kde leží `index.html`.

Doporučeno: nejdřív nasadit jako **preview**, projít kontrolu níže,
a teprve pak `--prod`.

### Kontrola PŘED nasazením

```bash
npx vercel deploy --dry
```

Vypíše seznam souborů, které se nahrají, a které se ignorují — nic se
přitom nenasadí. Zkontrolujte, že:

- v seznamu **je** `vercel.json`
- v seznamu **není** `README.md`

Tím se obě rizika z následující sekce odchytí dřív, než se cokoli nasadí.

---

## !! Dvě věci, které musí dorazit s weby !!

Obě selžou **tiše** — web bude vypadat správně, ale něco nebude fungovat.

1. **`vercel.json` musí být nasazený.** Obsahuje 19 přesměrování ze
   starých adres (`/o-nas`, `/nabidka`, `/kontakt`, `/obchodni-podminky` …).
   Bez něj všechny zaindexované adresy spadnou na 404 a web přijde
   o pozice ve vyhledávání.

2. **`.vercelignore` musí jet s ním.** Jinak budou `README.md`
   a `DEPLOY.md` veřejně čitelné.

---

## Kontrola po nasazení

```bash
for p in "" index.html o-nas nabidka programy-na-myti firmy novinky \
         kontakt obchodni-podminky login DEPLOY.md neexistuje-12345; do
  printf "%-26s " "/$p"
  curl -s -o /dev/null -w "%{http_code}  %{redirect_url}\n" "https://www.fxcarwash.cz/$p"
done
```

Očekávané:

| adresa | kód |
|---|---|
| `/` | 200 |
| `/index.html` | 301 → `/` |
| `/o-nas` … `/kontakt` | 301 → `/horni-briza.html#…` |
| `/obchodni-podminky` | 301 → `/obchodni-podminky.html` |
| `/login`, `/register`, `/profil` | 307 → `/` |
| `/DEPLOY.md` | 404 |
| `/neexistuje-12345` | **404** |

**`/neexistuje-12345` je nejspolehlivější kontrola.** Starý web na cokoli
vracel 200. Pokud vrací 404, nasazení se projevilo.

---

## Po nasazení

V Google Search Console odeslat `sitemap.xml` a požádat o indexaci
`/` a `/horni-briza.html`.

---

## Důležité před výměnou

**Zálohujte build původní aplikace** (v gitu nebo v historii nasazení
na Vercelu). Tato verze ho nahradí a dokud nebude hotová administrace,
je to jediná cesta, jak spravovat novinky na webu.
