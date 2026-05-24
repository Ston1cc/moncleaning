# CLAUDEPROMPT.md
## Șablon complet pentru site de companie de servicii (curățenie / amenajări / orice serviciu local)

---

## 1. Stack tehnic

- **HTML single-file** (`index.html`) — tot conținutul inline, fără framework
- **Tailwind CSS** via CDN: `<script src="https://cdn.tailwindcss.com"></script>`
- **Google Fonts** — Nunito (wght 400–900):
  ```html
  <link href="https://fonts.googleapis.com/css2?family=Nunito:wght@400;500;600;700;800;900&display=swap" rel="stylesheet" />
  ```
- **EmailJS** pentru emailuri automate fără backend:
  ```html
  <script src="https://cdn.jsdelivr.net/npm/@emailjs/browser@4/dist/email.min.js"></script>
  ```
- **Vercel Analytics**:
  ```html
  <script defer src="/_vercel/insights/script.js"></script>
  ```

---

## 2. Configurare Tailwind (în `<script>` după CDN)

```js
tailwind.config = {
  theme: {
    extend: {
      fontFamily: { sans: ['Nunito', 'sans-serif'] },
      colors: {
        brand: {
          50:  '#f0fdf4',
          100: '#dcfce7',
          200: '#bbf7d0',
          500: '#22c55e',
          600: '#16a34a',
          700: '#15803d',
        }
      }
    }
  }
}
```
> Schimbă culorile `brand` pentru orice altă identitate vizuală.

---

## 3. Structura secțiunilor (în ordine)

1. **Navbar** — logo, linkuri de navigație, buton CTA
2. **Hero** — heading principal, descriere, statistici, imagine staff/produs
3. **Service Cards** — 5–6 carduri cu iconițe SVG pentru fiecare serviciu
4. **Services Detail** — tabs (3 tipuri de servicii), imagine + checklist beneficii
5. **Google Reviews** — 3 carduri cu rating 5 stele și citat client
6. **Calculator CTA** — secțiune verde cu imagine și buton spre formular
7. **How We Work** — 4 pași numerotați
8. **UV Lamp / Serviciu Special** — secțiune dedicată unui serviciu premium (opțional)
9. **About Company** — paragraf SEO cu cuvinte cheie locale
10. **FAQ** — accordion cu 3–5 întrebări frecvente
11. **Price Form** — formular cu nume, telefon, serviciu, suprafață, dată, submit
12. **Footer** — logo, telefon, email, social icons (Instagram + Facebook)
13. **Success Modal** — popup animat după submit formular
14. **WhatsApp Float** — buton fix în colțul din dreapta-jos

---

## 4. Animații scroll (CSS + JS)

### CSS (în `<style>`):
```css
.reveal { opacity: 0; transform: translateY(36px); transition: opacity 0.6s ease, transform 0.6s ease; }
.reveal.visible { opacity: 1; transform: translateY(0); }
.reveal-left { opacity: 0; transform: translateX(-36px); transition: opacity 0.6s ease, transform 0.6s ease; }
.reveal-left.visible { opacity: 1; transform: translateX(0); }
.reveal-right { opacity: 0; transform: translateX(36px); transition: opacity 0.6s ease, transform 0.6s ease; }
.reveal-right.visible { opacity: 1; transform: translateX(0); }
.reveal-delay-1 { transition-delay: 0.1s; }
.reveal-delay-2 { transition-delay: 0.2s; }
.reveal-delay-3 { transition-delay: 0.3s; }
.reveal-delay-4 { transition-delay: 0.4s; }
```

### JS (în `<script>` la finalul body):
```js
const revealObserver = new IntersectionObserver((entries) => {
  entries.forEach(entry => {
    if (entry.isIntersecting) {
      entry.target.classList.add('visible');
      revealObserver.unobserve(entry.target);
    }
  });
}, { threshold: 0.12 });
document.querySelectorAll('.reveal, .reveal-left, .reveal-right').forEach(el => {
  revealObserver.observe(el);
});
```

---

## 5. Formular + notificări duale

### HTML form (câmpuri necesare):
```html
<form onsubmit="handleForm(event)">
  <input id="formName" type="text" placeholder="Nume" required />
  <input id="formPhone" type="tel" placeholder="069 000 000" required />
  <select id="formService" required>
    <option value="" disabled selected>Tip Serviciu</option>
    <!-- opțiuni servicii -->
  </select>
  <input id="formDate" type="date" required />
  <button type="submit">TRIMITE →</button>
</form>
```

### JS handler:
```js
const EMAILJS_SERVICE_ID  = 'service_XXXXXXX';  // din emailjs.com
const EMAILJS_TEMPLATE_ID = 'template_XXXXXXX';
const EMAILJS_PUBLIC_KEY  = 'XXXXXXXXXXXXXXXXX';
const OWNER_WHATSAPP      = '373XXXXXXXXX';      // număr internațional fără +

function handleForm(e) {
  e.preventDefault();
  const name    = document.getElementById('formName').value.trim();
  const phone   = document.getElementById('formPhone').value.trim();
  const service = document.getElementById('formService').value;
  const rawDate = document.getElementById('formDate').value;
  const [y, m, d] = rawDate.split('-');
  const date = `${d}.${m}.${y}`;

  // 1. Email automat către proprietar
  const emailMsg = `Cerere noua de pe site!\n\nNume: ${name}\nTelefon: +373 ${phone}\nServiciu: ${service}\nData: ${date}`;
  emailjs.send(EMAILJS_SERVICE_ID, EMAILJS_TEMPLATE_ID, { message: emailMsg }, EMAILJS_PUBLIC_KEY)
    .catch(err => console.error('EmailJS:', err));

  // 2. WhatsApp pre-completat pentru client
  const msg = `Cerere nouă de pe site! 🧹\n👤 Nume: ${name}\n📞 Telefon: +373 ${phone}\n🧽 Serviciu: ${service}\n📅 Data: ${date}`;
  window.open(`https://wa.me/${OWNER_WHATSAPP}?text=` + encodeURIComponent(msg), '_blank');

  // 3. Reset + modal
  e.target.reset();
  openModal();
}
```

### EmailJS setup:
1. Cont gratuit pe emailjs.com (200 emailuri/lună gratis)
2. Email Services → Add New Service → Zoho Mail (sau Gmail)
3. Email Templates → body: `{{message}}`, To Email: adresa proprietarului
4. Account → Public Key
5. Înlocuiește cele 3 constante de mai sus

---

## 6. Modal success

```html
<div id="successModal" class="fixed inset-0 z-[9999] flex items-center justify-center p-4 pointer-events-none opacity-0 transition-opacity duration-300">
  <div class="absolute inset-0 bg-black/40 backdrop-blur-sm" onclick="closeModal()"></div>
  <div id="successCard" class="relative bg-white rounded-3xl shadow-2xl p-8 max-w-sm w-full text-center transform scale-90 transition-transform duration-300">
    <!-- iconă verde, titlu, text, buton Închide -->
  </div>
</div>
```

```js
function openModal() {
  const modal = document.getElementById('successModal');
  const card  = document.getElementById('successCard');
  modal.classList.remove('pointer-events-none', 'opacity-0');
  modal.classList.add('pointer-events-auto', 'opacity-100');
  card.classList.remove('scale-90');
  card.classList.add('scale-100');
}
function closeModal() {
  const modal = document.getElementById('successModal');
  const card  = document.getElementById('successCard');
  modal.classList.add('pointer-events-none', 'opacity-0');
  modal.classList.remove('pointer-events-auto', 'opacity-100');
  card.classList.add('scale-90');
  card.classList.remove('scale-100');
}
```

---

## 7. WhatsApp buton flotant

```html
<a href="https://wa.me/373XXXXXXXXX" target="_blank" rel="noopener"
   class="fixed bottom-6 right-6 z-50 w-14 h-14 bg-[#25D366] rounded-full shadow-xl flex items-center justify-center hover:scale-110 transition-transform">
  <!-- SVG WhatsApp icon -->
</a>
```

---

## 8. Favicon

```html
<link rel="icon" type="image/jpeg" href="img/logo.jpeg" />
```

---

## 9. Deploy — GitHub + Vercel

```bash
git init
git add .
git commit -m "Initial commit"
git remote add origin https://github.com/USER/REPO.git
git push -u origin main
```
- Import repo în Vercel → auto-deploy la fiecare push pe `main`
- Settings → Domains → adaugă domeniul custom

---

## 10. DNS pentru domeniu custom (.md sau altele)

### Dacă registrarul permite editare DNS directă:
| Tip | Nume | Valoare |
|-----|------|---------|
| `A` | `@` | `76.76.21.21` |
| `CNAME` | `www` | `cname.vercel-dns.com` |

### Dacă registrarul NU permite editare fără propriile nameservere:
Setează nameservere la **alfa.dns.md** și **beta.dns.md** (sau echivalentul registrarului), editează zona DNS acolo.

> **Evită** să ai simultan nameservere Vercel + nameservere registrar — creează conflict SSL.

---

## 11. Email profesional (Zoho Mail — gratuit)

1. zoho.com/mail → Forever Free Plan → Add existing domain
2. Verificare domeniu: adaugă TXT record în DNS
3. MX records de adăugat:
   | Tip | Valoare | Prioritate |
   |-----|---------|-----------|
   | MX | mx.zoho.eu | 10 |
   | MX | mx2.zoho.eu | 20 |
   | MX | mx3.zoho.eu | 50 |
4. SPF: `v=spf1 include:zohomail.eu ~all`
5. DKIM: generat automat de Zoho în setup
6. Forwarding opțional: mail.zoho.eu → Settings → Mail Accounts → Forwards

---

## 12. Imagini recomandate

| Secțiune | Imagine |
|----------|---------|
| Hero | Staff în uniformă / echipă la lucru |
| Services | Banner promo serviciu principal |
| Calculator CTA | Serviciu specific (ex. curățare canapea) |
| Price List | Tabel prețuri al companiei |
| Logo | Fișier circular, folosit în navbar + footer + favicon |

---

## 13. SEO de bază

```html
<title>Nume Companie — Serviciu în Oraș</title>
<meta name="description" content="Descriere scurtă 150 caractere cu cuvinte cheie locale." />
<!-- Open Graph pentru preview pe WhatsApp/Facebook -->
<meta property="og:title" content="Nume Companie" />
<meta property="og:description" content="Descriere serviciu" />
<meta property="og:image" content="https://domeniu.md/img/logo.jpeg" />
<meta property="og:url" content="https://domeniu.md" />
```

---

## 14. Checklist final înainte de livrare

- [ ] Toate textele sunt în limba clientului
- [ ] Numărul de telefon corect în navbar, footer, WhatsApp float, form
- [ ] Email corect în footer
- [ ] Linkuri Instagram + Facebook funcționale
- [ ] Formular testat — email ajunge la proprietar, WhatsApp se deschide
- [ ] Favicon apare în tab browser
- [ ] Site funcțional pe mobil
- [ ] Domeniu custom conectat cu SSL valid
- [ ] Zoho Mail configurat și forwarding activ
- [ ] Google Search Console — indexare solicitată
- [ ] Vercel Analytics activat în dashboard
- [ ] Contract semnat de ambele părți
