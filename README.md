# Algorytmy-maturalne-part-II  
# Algorytmy na tekstach oraz kompresji i szyfrowania  
**Windows Forms (.NET, C#)**

Projekt edukacyjny prezentujący wybrane algorytmy:
- przetwarzania tekstu,
- kompresji informacji,
- szyfrowania i podpisu elektronicznego,

zrealizowane w postaci aplikacji **Windows Forms**.

---

## 1. Algorytmy na tekstach

### 1.1 Sprawdzanie palindromu
Algorytm sprawdza, czy dany ciąg znaków czytany od początku i od końca jest identyczny.

**Złożoność:**  
- czasowa: `O(n)`  
- pamięciowa: `O(1)`

---

### 1.2 Sprawdzanie anagramu
Algorytm sprawdza, czy dwa ciągi znaków składają się z tych samych liter z taką samą częstością.

**Metoda:**  
- zliczanie wystąpień znaków (tablica / słownik)

**Złożoność:**  
- czasowa: `O(n)`  
- pamięciowa: `O(1)` (dla ASCII)

---

### 1.3 Porządkowanie alfabetyczne (leksykograficzne)
Sortowanie listy słów zgodnie z porządkiem słownikowym.

---

### 1.4 Wyszukiwanie wzorca w tekście
Algorytm wyszukuje wszystkie wystąpienia wzorca w tekście.

**Złożoność:**  
- czasowa: `O(n·m)`  

---

### 1.5 Odwrotna notacja polska (ONP / RPN)
Algorytm oblicza wartość wyrażenia zapisanego w odwrotnej notacji polskiej.

---

## 2. Algorytmy kompresji i szyfrowania

### 2.1 Kody znaków o zmiennej długości
- alfabet Morse’a  
- kod Huffmana  

---

### 2.2 Szyfr Cezara
Szyfr podstawieniowy polegający na przesunięciu liter alfabetu o stałą wartość `k`.

---

### 2.3 Szyfr przestawieniowy
Szyfr oparty na zmianie kolejności znaków.

---

### 2.4 RSA i podpis elektroniczny
Algorytm kryptografii asymetrycznej wykorzystywany do podpisu elektronicznego
i weryfikacji autentyczności danych.

---

## 3. Technologie

- **Język:** C#  
- **Framework:** .NET  
- **Interfejs:** Windows Forms  
- **Biblioteki:** `System.Security.Cryptography`

---

## 4. Kod aplikacji (Form1.cs)

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Windows.Forms;
using System.Security.Cryptography;

namespace AlgorithmsTextCrypto
{
    public partial class Form1 : Form
    {
        public Form1()
        {
            InitializeComponent();
        }

        private bool IsPalindrome(string s)
        {
            int i = 0, j = s.Length - 1;
            while (i < j)
            {
                if (s[i] != s[j]) return false;
                i++; j--;
            }
            return true;
        }

        private bool IsAnagram(string a, string b)
        {
            if (a.Length != b.Length) return false;
            int[] count = new int[256];
            foreach (char c in a) count[(byte)c]++;
            foreach (char c in b)
                if (--count[(byte)c] < 0) return false;
            return true;
        }

        private string SortLexicographically(string text)
        {
            var words = text.Split(
                new[] { ' ', '\n', '\r', '\t' },
                StringSplitOptions.RemoveEmptyEntries);

            Array.Sort(words, StringComparer.CurrentCulture);
            return string.Join(Environment.NewLine, words);
        }

        private List<int> FindPattern(string text, string pattern)
        {
            var positions = new List<int>();
            for (int i = 0; i + pattern.Length <= text.Length; i++)
            {
                int j = 0;
                while (j < pattern.Length && text[i + j] == pattern[j]) j++;
                if (j == pattern.Length) positions.Add(i);
            }
            return positions;
        }

        private double EvaluateRPN(string expression)
        {
            var stack = new Stack<double>();
            foreach (var token in expression.Split(' '))
            {
                if (double.TryParse(token, out double number))
                    stack.Push(number);
                else
                {
                    double b = stack.Pop();
                    double a = stack.Pop();
                    stack.Push(token switch
                    {
                        "+" => a + b,
                        "-" => a - b,
                        "*" => a * b,
                        "/" => a / b,
                        _ => throw new Exception("Nieznany operator")
                    });
                }
            }
            return stack.Pop();
        }

        private string CaesarCipher(string text, int key)
        {
            key = ((key % 26) + 26) % 26;
            return new string(text.Select(c =>
            {
                if (char.IsLower(c))
                    return (char)('a' + (c - 'a' + key) % 26);
                if (char.IsUpper(c))
                    return (char)('A' + (c - 'A' + key) % 26);
                return c;
            }).ToArray());
        }

        private RSA rsa = RSA.Create();
        private byte[] signature;
    }
}
```

### 📥 Pola tekstowe

- `textBoxInput` – główne pole wejściowe (tekst / wyrażenie)
- `textBoxInput2` – drugie pole wejściowe (np. anagram)
- `textBoxPattern` – wzorzec do wyszukiwania w tekście
- `textBoxOutput` – pole wyjściowe (wynik działania algorytmu)
- `numericUpDownKey` – klucz szyfrowania (np. szyfr Cezara)

---

### 🔘 Przyciski

- `buttonPalindrome` – sprawdzanie palindromu
- `buttonAnagram` – sprawdzanie anagramu
- `buttonSort` – sortowanie leksykograficzne
- `buttonFind` – wyszukiwanie wzorca w tekście
- `buttonRpn` – obliczanie wyrażenia w ONP (RPN)
- `buttonCaesar` – szyfrowanie szyfrem Cezara
- `buttonSign` – generowanie podpisu elektronicznego (RSA)
- `buttonVerify` – weryfikacja podpisu elektronicznego

---

⚠️ **Uwaga:**  
Zmiana nazw kontrolek w `Designer.cs` lub w edytorze wizualnym spowoduje błędy kompilacji,  
jeśli nie zostaną one zsynchronizowane z kodem w pliku `Form1.cs`.

## 📝 Zadanie samodzielne  
### Zamiana liczby dwójkowej na dziesiętną (BIN → DEC)

**Treść zadania:**

Napisz program, który zamienia liczbę zapisaną w systemie dwójkowym (BIN)
na liczbę zapisaną w systemie dziesiętnym (DEC).

Program powinien:
1. Pobierać od użytkownika liczbę binarną składającą się wyłącznie z cyfr `0` i `1`.
2. Sprawdzać poprawność danych wejściowych.
3. Obliczać wartość liczby dziesiętnej **bez użycia gotowych funkcji konwersji**.
4. Wyświetlać wynik w systemie dziesiętnym.

---


