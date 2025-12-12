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

**Implementacja:**  
- wbudowana metoda `Array.Sort()`  
- porównywanie leksykograficzne

---

### 1.4 Wyszukiwanie wzorca w tekście
Algorytm wyszukuje wszystkie wystąpienia wzorca w tekście.

**Zastosowany algorytm:**  
- naiwny (porównywanie znak po znaku)

**Złożoność:**  
- czasowa: `O(n·m)`  
- gdzie `n` – długość tekstu, `m` – długość wzorca

---

### 1.5 Odwrotna notacja polska (ONP / RPN)
Algorytm oblicza wartość wyrażenia zapisanego w odwrotnej notacji polskiej.

**Metoda:**  
- wykorzystanie struktury danych **stos**

**Obsługiwane operatory:**  
`+  -  *  /`

---

## 2. Algorytmy kompresji i szyfrowania

### 2.1 Kody znaków o zmiennej długości
- alfabet Morse’a  
- kod Huffmana  

**Huffman – idea działania:**
1. zliczenie częstości znaków,
2. budowa drzewa binarnego (kolejka priorytetowa),
3. generowanie kodów binarnych na podstawie drzewa.

---

### 2.2 Szyfr Cezara
Szyfr podstawieniowy polegający na przesunięciu liter alfabetu o stałą wartość `k`.

**Cechy:**
- prosty do implementacji,
- niski poziom bezpieczeństwa,
- przykład historycznego szyfrowania.

---

### 2.3 Szyfr przestawieniowy
Szyfr oparty na zmianie kolejności znaków (np. szyfr kolumnowy z kluczem).

**Cechy:**
- brak zmiany znaków,
- zmieniana jest jedynie ich pozycja w tekście.

---

### 2.4 RSA i podpis elektroniczny
Algorytm kryptografii asymetrycznej wykorzystywany m.in. do:
- szyfrowania,
- podpisu elektronicznego,
- weryfikacji autentyczności danych.

**Implementacja w .NET:**
- `RSA`
- `SHA256`
- `SignData()`
- `VerifyData()`

---

## 3. Technologie

- **Język:** C#  
- **Framework:** .NET  
- **Interfejs:** Windows Forms  
- **Biblioteki kryptograficzne:**  
  `System.Security.Cryptography`

---

## 4. Elementy interfejsu Windows Forms

W projekcie wykorzystywane są m.in.:

- `Form`
- `Label`
- `TextBox`
- `RichTextBox`
- `Button`
- `ComboBox`
- `ListBox`
- `NumericUpDown`
- `MessageBox`
- `OpenFileDialog`
- `SaveFileDialog`

---

## 5. Struktura aplikacji

Aplikacja składa się z:
- pól wejściowych dla danych tekstowych,
- przycisków uruchamiających poszczególne algorytmy,
- pola wynikowego prezentującego rezultat działania algorytmu.

---

## 6. Cel projektu

Celem projektu jest:
- praktyczne poznanie klasycznych algorytmów,
- zrozumienie podstaw przetwarzania tekstu i kryptografii,
- nauka implementacji algorytmów w aplikacji okienkowej .NET.

---


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

        // =========================================================
        // 1. ALGORYTMY NA TEKSTACH
        // =========================================================

        // Palindrom
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

        // Anagram
        private bool IsAnagram(string a, string b)
        {
            if (a.Length != b.Length) return false;

            int[] count = new int[256];
            foreach (char c in a) count[(byte)c]++;
            foreach (char c in b)
                if (--count[(byte)c] < 0) return false;

            return true;
        }

        // Sortowanie leksykograficzne
        private string SortLexicographically(string text)
        {
            var words = text.Split(
                new[] { ' ', '\n', '\r', '\t' },
                StringSplitOptions.RemoveEmptyEntries);

            Array.Sort(words, StringComparer.CurrentCulture);
            return string.Join(Environment.NewLine, words);
        }

        // Wyszukiwanie wzorca w tekście (naiwne)
        private List<int> FindPattern(string text, string pattern)
        {
            var positions = new List<int>();

            for (int i = 0; i + pattern.Length <= text.Length; i++)
            {
                int j = 0;
                while (j < pattern.Length && text[i + j] == pattern[j])
                    j++;

                if (j == pattern.Length)
                    positions.Add(i);
            }
            return positions;
        }

        // Odwrotna notacja polska (ONP)
        private double EvaluateRPN(string expression)
        {
            var stack = new Stack<double>();

            foreach (var token in expression.Split(' '))
            {
                if (double.TryParse(token, out double number))
                {
                    stack.Push(number);
                }
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

        // =========================================================
        // 2. SZYFROWANIE
        // =========================================================

        // Szyfr Cezara
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

        // =========================================================
        // 3. RSA – PODPIS ELEKTRONICZNY
        // =========================================================

        private RSA rsa = RSA.Create();

        private byte[] SignMessage(string message)
        {
            byte[] data = Encoding.UTF8.GetBytes(message);
            return rsa.SignData(
                data,
                HashAlgorithmName.SHA256,
                RSASignaturePadding.Pkcs1);
        }

        private bool VerifyMessage(string message, byte[] signature)
        {
            byte[] data = Encoding.UTF8.GetBytes(message);
            return rsa.VerifyData(
                data,
                signature,
                HashAlgorithmName.SHA256,
                RSASignaturePadding.Pkcs1);
        }

        private byte[] signature;

        // =========================================================
        // 4. OBSŁUGA PRZYCISKÓW (EVENTY)
        // =========================================================

        private void buttonPalindrome_Click(object sender, EventArgs e)
        {
            textBoxOutput.Text = IsPalindrome(textBoxInput.Text)
                ? "TAK – palindrom"
                : "NIE – nie palindrom";
        }

        private void buttonAnagram_Click(object sender, EventArgs e)
        {
            textBoxOutput.Text = IsAnagram(textBoxInput.Text, textBoxInput2.Text)
                ? "TAK – anagram"
                : "NIE – nie anagram";
        }

        private void buttonSort_Click(object sender, EventArgs e)
        {
            textBoxOutput.Text = SortLexicographically(textBoxInput.Text);
        }

        private void buttonFind_Click(object sender, EventArgs e)
        {
            var positions = FindPattern(textBoxInput.Text, textBoxPattern.Text);
            textBoxOutput.Text = positions.Count > 0
                ? "Znaleziono na pozycjach: " + string.Join(", ", positions)
                : "Brak wystąpień";
        }

        private void buttonRpn_Click(object sender, EventArgs e)
        {
            try
            {
                double result = EvaluateRPN(textBoxInput.Text);
                textBoxOutput.Text = result.ToString();
            }
            catch
            {
                textBoxOutput.Text = "Błąd wyrażenia ONP";
            }
        }

        private void buttonCaesar_Click(object sender, EventArgs e)
        {
            int key = (int)numericUpDownKey.Value;
            textBoxOutput.Text = CaesarCipher(textBoxInput.Text, key);
        }

        private void buttonSign_Click(object sender, EventArgs e)
        {
            signature = SignMessage(textBoxInput.Text);
            textBoxOutput.Text = Convert.ToBase64String(signature);
        }

        private void buttonVerify_Click(object sender, EventArgs e)
        {
            bool valid = VerifyMessage(textBoxInput.Text, signature);
            textBoxOutput.Text = valid
                ? "Podpis poprawny"
                : "Podpis niepoprawny";
        }
    }
}

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
