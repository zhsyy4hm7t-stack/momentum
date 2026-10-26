# MOMENTUM – Supabase + Vercel

Proiect static (fără build): `index.html`, `config.js`, `vercel.json`, `supabase/`.

## 1. Supabase (făcut de dumneavoastră)
1. supabase.com → **New project** (regiune UE, ex. Frankfurt). Salvați parola bazei de date.
2. **SQL Editor** → lipiți tot `supabase/schema.sql` → **Run**.
3. **Authentication → Sign In / Providers**: dezactivați **Allow new users to sign up**.
4. **Authentication → Users → Add user**: creați contul de personal (e-mail + parolă puternică).
5. În SQL Editor: `insert into public.staff (user_id,email) select id,email from auth.users where email='EMAILUL_PERSONALULUI';`
6. **Project Settings → API**: copiați *Project URL* și cheia *anon/publishable* în `config.js`. Nu copiați `service_role`/`secret`.

## 2. E-mailuri (făcut de dumneavoastră)
1. Cont la resend.com, verificați domeniul companiei, creați o cheie API.
2. Instalați Supabase CLI, apoi:
   ```
   supabase login && supabase link --project-ref REF_PROIECT
   supabase secrets set RESEND_API_KEY=... FROM_EMAIL="MOMENTUM <rezervari@domeniul-dvs.ro>" STAFF_EMAIL=... WEBHOOK_SECRET=un_sir_lung_aleatoriu
   supabase functions deploy notify --no-verify-jwt
   ```
3. **Database → Webhooks → Create**: tabel `bookings`, evenimente **Insert** și **Update**, tip *HTTP Request* POST către URL-ul funcției `notify`, header `x-webhook-secret` = același `WEBHOOK_SECRET`.

## 3. Vercel (făcut de dumneavoastră)
Urcați folderul pe GitHub → vercel.com → **Add New Project** → importați depozitul (Framework: *Other*, fără build command). Linkul public apare după deploy.

## 4. Teste obligatorii înainte de lansare
- Trimiteți o cerere de pe iPhone; verificați rândul în *Table Editor → bookings* și e-mailul primit.
- Încercați aceeași dată/interval din alt browser: trebuie să apară „ocupat".
- Autentificați-vă ca personal; confirmați, reprogramați, anulați; verificați e-mailurile de status.
- Fără autentificare, în consola browserului: `sb.from("bookings").select()` trebuie să returneze gol/eroare.
- Un cont autentificat care nu e în `staff` nu trebuie să vadă nimic.

## Limite cunoscute
Fără încărcare de fotografii; capacitatea per interval este 1 (modificabilă în `time_slots.capacity`); datele clienților sunt protejate prin RLS, TLS și criptarea discului Supabase, nu prin criptare la nivel de coloană.
