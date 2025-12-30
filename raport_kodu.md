Raport - mObywatel - Ui...

Data: 2025-12-30

**

To repozytorium **NIE zawiera**:
- Logiki biznesowej aplikacji
- Komunikacji z API rządowym
- Mechanizmów uwierzytelniania
- Kodu do weryfikacji dokumentów
- Kodu do podpisywania cyfrowego
- Integracji z systemami rządowymi
- Pełnej struktury projektu Gradle/Android

## Wnioski

1. **To Design System, nie pełna aplikacja** - zawiera tylko komponenty UI
2. **Kod jakościowy** - profesjonalnie napisany, zgodnie z best practices Kotlin/Compose
3. **Bezpieczeństwo podstawowe** - maskowanie PINów, walidacja, ale faktyczne bezpieczeństwo zależy od backendu
4. **Open source przez COI** - pozytywny krok dla transparentności rządu
5. **Niepełny mirror** - brakuje kluczowych elementów aplikacji


**mObywatel-mirror** to **nieoficjalne lustro** kodu źródłowego aplikacji **mObywatel** - oficjalnej aplikacji mobilnej polskiego rządu do cyfrowych dokumentów tożsamości.

## Informacje podstawowe

| Parametr | Wartość |
|----------|---------|
| **Właściciel kodu** | Centralny Ośrodek Informatyki (COI) |
| **Licencja** | MIT License (2025) |
| **Autor mirror** | @neziw (GitHub) |
| **Język** | Kotlin |
| **Framework** | Jetpack Compose (Android) |
| **Liczba plików .kt** | 189 plików |
| **Łączna liczba linii** | ~19,440 linii kodu |
| **Status** | Nieoficjalna kopia udostępniona publicznie przez COI |
| **Źródło** | https://github.com/neziw/mObywatel-mirror |

## Struktura projektu

To **biblioteka komponentów UI (Design System)** dla aplikacji mObywatel, NIE pełna aplikacja. Zawiera wyłącznie komponenty interfejsu użytkownika.

### Główne pakiety:

```
pl.gov.coi.common.ui.ds.*
├── accordion/          - Komponenty rozwijane (akordeony)
├── alert/              - Alerty i powiadomienia
├── badge/              - Znaczniki i etykiety
├── banner/             - Bannery informacyjne
├── bottomnavigation/   - Nawigacja dolna
├── bottomsheet/        - Modalne arkusze dolne
├── button/             - Przyciski (text, icon, dropdown)
├── checkbox/           - Pola wyboru (checkboxy)
├── radiobutton/        - Przyciski radiowe
├── textinput/          - Pola tekstowe (input, textarea, PIN)
├── singlecard/         - Karty dokumentów
├── topappbar/          - Górne paski nawigacyjne
├── dialog/             - Dialogi modalne
├── filepicker/         - Wybór plików
├── searchbar/          - Paski wyszukiwania
├── snackbar/           - Powiadomienia tymczasowe
└── custom/             - Niestandardowe komponenty
    ├── barcode/        - Kody kreskowe/QR
    ├── icon/           - Ikony
    └── documentrow/    - Wiersze dokumentów
```

## Komponenty bezpieczeństwa

### 1. TextFieldPin (PIN input)
- Pole do wprowadzania kodu PIN
- Obsługa tylko cyfr (`isDigitsOnly()`)
- Maskowanie: wyświetla kropki (•) zamiast cyfr
- Walidacja długości PIN
- **Lokalizacja**: `TextFieldPin.kt:42-86`

```kotlin
private const val DOT_CHAR = '\u2022'  // Maskowanie PINu
onValueChange = {
  if (it.text.length <= data.length && it.text.isDigitsOnly()) {
    data.onValueChanged(it.text)
  }
}
```

### 2. TextField (ogólne pole tekstowe)
- Obsługa hasła z `PasswordVisualTransformation`
- Możliwość ukrywania/pokazywania hasła
- Walidacja stanów (Invalid/Valid)
- Focus management dla bezpieczeństwa wprowadzania
- **Lokalizacja**: `TextField.kt:71-100`

```kotlin
var isPasswordVisible by remember { mutableStateOf(false) }
// PasswordVisualTransformation dla pól hasła
```

### 3. BarCodeSingleCard (kod kreskowy)
- Wyświetlanie kodów kreskowych dokumentów
- Dekodowanie Base64 → Bitmap
- Brak szyfrowania (dane przychodzą już zakodowane Base64)
- **Lokalizacja**: `BarCodeSingleCard.kt:32-75`

```kotlin
private fun provideBitmap(value: String): Bitmap {
  val picture = Base64.decode(value, Base64.NO_WRAP)
  return BitmapFactory.decodeByteArray(picture, 0, picture.size)
}
```

## Obserwacje bezpieczeństwa

### ✅ Dobre praktyki:
- Maskowanie PINów (kropki zamiast cyfr)
- Walidacja tylko cyfr w polach PIN
- Obsługa PasswordVisualTransformation dla haseł
- Walidacja stanów (ValidationState.Invalid)
- Brak hardcodowanych sekretów/kluczy API w tym kodzie

### ⚠️ Uwagi:
- **To tylko UI library** - faktyczne bezpieczeństwo zależy od implementacji backendu
- Base64 to tylko **kodowanie, nie szyfrowanie** - dane kodów kreskowych nie są chronione kryptograficznie w tym kodzie
- Brak widocznego kodu do komunikacji sieciowej (prawdopodobnie w innych modułach aplikacji)
- **Niepełny kod** - według README: "Few files may be missing!"

## Technologie

| Technologia | Zastosowanie |
|-------------|--------------|
| **Kotlin** | Język programowania |
| **Jetpack Compose** | Framework UI (deklaratywny) |
| **Android SDK** | Platforma mobilna |
| **Material Design** | System projektowania |
| **AppTheme** | Własny theme system (kolory, typografia, dimensions) |

## Kluczowe pliki (TOP 10)

| Plik | Rozmiar | Opis |
|------|---------|------|
| CardListPreviewParameterProvider.kt | 33 KB | Dane testowe dla list kart |
| SingleCardPreviewParameterProvider.kt | 23 KB | Dane testowe dla pojedynczych kart |
| AlertPreviewParameterProvider.kt | 17 KB | Dane testowe dla alertów |
| SingleCardInfo.kt | 16 KB | Komponent karty informacyjnej |
| TextField.kt | 14 KB | Pole tekstowe z walidacją |
| ButtonData.kt | 14 KB | Modele danych przycisków |
| TextInputData.kt | 13 KB | Modele danych pól tekstowych |
| SingleCardData.kt | 13 KB | Modele danych kart |
| ChatBubble.kt | 13 KB | Komponent dymków czatu |
| TextArea.kt | 12 KB | Pole tekstowe wieloliniowe |

## Architektura komponentów

Komponenty są zbudowane według wzorca:

```
[Komponent].kt          - Komponent Composable (@Composable fun)
[Komponent]Data.kt      - Klasa modelu danych (data class)
[Komponent]PPP.kt       - Preview Parameter Provider (podgląd w Android Studio)
```

Przykład: `Button.kt`, `ButtonData.kt`, `ButtonPPP.kt`

## Licencja MIT (COI 2025)

Kod jest dostępny na **licencji MIT** od Centralnego Ośrodka Informatyki. Oznacza to:
- ✅ Wolne użytkowanie, kopiowanie, modyfikowanie
- ✅ Komercyjne wykorzystanie dozwolone
- ✅ Sublicencjonowanie dozwolone
- ⚠️ BEZ GWARANCJI ("AS IS")
- ✅ Wymóg: dołączenie informacji o prawach autorskich



## Struktura katalogów w mirror

```
mObywatel-mirror/
├── .git/
├── .gitattributes
├── LICENSE.txt
├── README.md
└── [189 plików .kt - komponenty UI]
```

## Przykładowe komponenty

### Button (Przycisk)
- ButtonIcon - przycisk z ikoną
- ButtonText - przycisk tekstowy
- ButtonData - model danych
- Obsługa stanów: enabled/disabled
- Różne warianty: primary, secondary, tertiary

### SingleCard (Karta dokumentu)
- Wyświetlanie dokumentów tożsamości
- Obsługa kodów kreskowych/QR
- Status badge (aktywny, nieaktywny, wygasły)
- Możliwość wyboru (checkbox/radiobutton)
- Info rows (pola informacyjne)

### TextInput (Pole tekstowe)
- TextField - pole standardowe
- TextFieldPin - pole PIN (tylko cyfry, maskowane)
- TextFieldPhoneNumber - pole numeru telefonu
- TextArea - pole wieloliniowe
- Walidacja i obsługa błędów

## Źródła

- [mObywatel-mirror GitHub](https://github.com/neziw/mObywatel-mirror)