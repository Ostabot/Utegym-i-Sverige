# Utegym i Sverige

Expo/React Native-app för att upptäcka utegym, planera träningspass och dela pass genom Supabase.

## Kvalitetsplan inför TestFlight / release

### Autentisering

- **Google login**  
  - Implementerad via Supabase OAuth.
  - Redirect/deeplink: `utegym://auth/callback` (registrerad som `scheme` i `app.config.ts` och som Redirect URL i Supabase).
  - Testat i simulator/dev-client med Tobias som test user i Google-konsolen.

- **Sign in with Apple**  
  - *Planerad, ej implementerad ännu.*  
  - Krav inför App Store **production**, men inte för första TestFlight-rundorna.
  - Att göra:
    - Aktivera **Sign in with Apple** på bundle identifier `org.name.utegymisverige` i Apple Developer.
    - Lägga till Apple som provider i Supabase (Services ID + redirect).
    - Implementera sign-in-knapp i appen och koppla till Supabase.

### Monitoring / loggar

- **Sentry**  
  - *Planerad, ej aktiv just nu.*  
  - Tidigare test med `sentry-expo` backades ur p.g.a. pod/RC-Folly-strul efter uppgradering till React Native 0.81.
  - Plan:
    - Återinföra Sentry när RN/Expo + Sentry har stabil integration.
    - Starta med endast JS-errors + basic performance (ingen native-customisering i första skedet).

### Testning

- **E2E-tester (Detox)**  
  - *Planerad, ej implementerad ännu.*  
  - Mål på sikt:
    1. Skapa pass (från gym → välj utrustning → generera pass).
    2. Markera sets som genomförda.
    3. Avsluta pass.
    4. Kontrollera att passet dyker upp i historiken.
  - Initialt fokuserar vi på manuella “happy path”-tester innan varje TestFlight-build.

### Manuella release-tester (kort checklista)

- Starta appen online och offline.
- Hitta gym nära mig (location + karta).
- Generera ett pass och köra igenom hela flödet.
- Testa Google-login från:
  - kallstart
  - när appen redan är öppen.
- Logga ut / logga in igen.
- Kontrollera att inga uppenbara krascher eller blockers finns.


## Funktioner

- Expo Router (tabs/stacks) för Hem, Träna, Flöde och Profil.
- Supabase-klient med SecureStore + AsyncStorage för sessioner och deep-link bootstrap (`utegym://auth/callback`).
- Hemskärm med sök, stadsfilter, betygsfilter och distanssortering när plats finns.
- Gymdetaljer med utrustning, foton och foto-uppladdning till Supabase Storage.
- Träningsguide (5 steg) med React Hook Form + Zod, offline-draft i AsyncStorage.
- Passkörning med loggning av set/reps/vikt/RPE, autosparning lokalt och offline-sync till Supabase.
- Flöde med filtrering på gym och datum, träningsdetaljer.
- Profil med magisk länk/OAuth, alias-hantering, dark mode och pending-sync översikt.
- Grundläggande tests för helper-funktioner (`npm test`).

## Offline & synk

- Aktiva pass sparas lokalt (`AsyncStorage`).
- När sparande misslyckas (offline) läggs passet i en pending-lista.
- `usePendingWorkoutSync` försöker synka så fort nätverk finns (NetInfo).

## Design & UI

- Stilad med StyleSheet och konsistenta komponenter.
- Toasts via `react-native-toast-message` för fel/success.
- Haptics på viktiga actions (starta pass från gymdetalj).

## Deep links

- Magic links och OAuth använder `utegym://auth/callback`.
- `AuthBootstrapper` lyssnar på Expo Linking + web URL för att byta kod mot session.

