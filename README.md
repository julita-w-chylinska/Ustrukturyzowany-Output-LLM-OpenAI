# Ustrukturyzowany-LLM-OpenAI

Duże modele językowe (Large Language Models, LLM) mają to do siebie, że przy użyciu tych samych promptów generują różne odpowiedzi, nie tylko pomiędzy różnymi modelami [tu ewentualnie odniesienie do screenshotów], ale też używając tego samego modelu, jako że odpowiedź w pewnym stopniu jest oparta na losowości. 

Jest to przeszkodą, jeśli chcemy za każdym razem otrzymywać taką samą strukturę odpowiedzi. Dodatkowo LLM’y mogą generować różną ilość tekstu i podawać informacje, o które nie do końca nam chodzi. Z pomocą przychodzi tu API (Application Programming Interface, Interfejs Programowania Aplikacji) i dostępne w nim Function Calling i Structured Output. 

Ten projekt ma postać testowania możliwości tych funkcjonalności i będzie dotyczył API OpenAI, modelu gpt-4o-mini-2024-07-18. Używam do niego języka polskiego ze względu na używanie tego języka do promptów/inputów.


## Pięć niezbędnych kroków do rozpoczęcia pracy z API OpenAI w VS Code [ogólnie nazwać]

1. Utworzenie płatnego klucza API na platformie OpenAI

https://platform.openai.com/api-keys

3. Instalacja biblioteki (?) openai w VS Code [ogólnie nazwać]

```Python
pip install openai
```
3. Ustawienie osobistego klucza API w zmiennych środowiskowych

```
setx OPENAI_API_KEY "TWÓJ_KLUCZ_TUTAJ"
```

4. Import API od OpenAI

```Python
from openai import OpenAI
```

5. Zdefiniowanie klienta? Jak to nazwać?

``` Python
client = OpenAI()
```
## Pierwsze próby wygenerowania odpowiedzi

```Python
resp = client.responses.create(
    model="gpt-4o-mini-2024-07-18",
    input="Test: jeśli to działa, odpowiedz jednym zdaniem po polsku."
)

print(resp.output_text)
```

```Python
Działa, wszystko w porządku!
```
## Jak na pytanie "Gdzie mieszka Smok Wawelski?" odpowiedzą trzy różne modele (z różnych UI)

ChatGPT
  
<img width="700" alt="image" src="https://github.com/user-attachments/assets/aff8ef7d-2f6c-4c67-a50f-45f3a486fbec" />

Perpexity
  
<img width="700" alt="image" src="https://github.com/user-attachments/assets/a32b35f0-af01-45e0-afa9-b0e4e3a38ad1" />
  
(w tym przypadku jeszcze propozycje dopytania)

deepseek
  
<img width="900" alt="image" src="https://github.com/user-attachments/assets/6ca11826-c42f-4437-960a-09e25d6970bc" />
  
## Jak na to pytanie odpowiedziałby LLM z użyciem API?

```Python
response = client.chat.completions.create(
    model = "gpt-4o-mini-2024-07-18",
    messages = [
        {"role": "user", "content": "Gdzie mieszka Smok Wawelski?"}
    ]
)

response.choices[0].message.content
```

```Python
'Smok Wawelski, legenda związana z Krakowem, mieszkał w jaskini znajdującej się pod Wawelem, na wzgórzu Wawelskim. Jaskinia, znana jako "Smokowa Jama", jest teraz popularną atrakcją turystyczną. W legendach smok terroryzował mieszkańców Krakowa, aż został pokonany przez księcia Kraka, co zakończyło jego rządy terroru. Współczesny Kraków często wspomina tę legendę, a także można tam znaleźć pomnik Smoka Wawelskiego, który czasami "zionie" ogniem.'
```
  
## A gdyby zadać mu dokładnie takie samo pytanie 5 razy z rzędu?
  
```Python
response = client.chat.completions.create(
    model = "gpt-4o-mini-2024-07-18",
    messages = [
        {"role": "user", "content": "Gdzie mieszka Smok Wawelski?"}
    ],
    n = 5
)

for ch in response.choices:
    print(ch.message.content)
    print("=" * 10)
```
  
Output:
```Python
Smok Wawelski mieszka na Wawelu, w Krakowie, w Polsce. Jego legendarna jaskinia znajduje się pod Wzgórzem Wawelskim, w pobliżu zamku królewskiego. W rzeczywistości nie ma konkretnego adresu dla smoka, ponieważ jest to postać z legendy, ale ogólnie można powiedzieć, że jego „dom” to Zamek Wawelski, Wawel 5, 31-001 Kraków, Polska.
==========
Smok Wawelski mieszka w Krakowie, pod Wawelem, przy Wawel 5. To miejsce jest znane z legendy o Smoku Wawelskim, który według tradycji zamieszkiwał jaskinię pod Wzgórzem Wawelskim.
==========
Smok Wawelski mieszka na Wawelu, w Krakowie, w Polsce. Jego legendarne miejsce zamieszkania to jaskinia w pobliżu Wawelskiego Zamku, w okolicach brzegu Wisły. Dokładny adres to: Wawel 5, 31-001 Kraków, Polska.
==========
Smok Wawelski mieszka w Krakowie, a jego legenda związana jest z Wzgórzem Wawelskim. Wszyscy znają go jako mieszkańca pod Wawelskim zamkiem, który według legendy miał terroryzować okoliczne tereny. Adres, który można podać, to: Wawel, 31-001 Kraków, Polska.
==========
Smok Wawelski mieszka w Krakowie, a jego legendarna siedziba znajduje się pod Wzgórzem Wawelskim, na którym stoi Zamek Królewski na Wawelu. Jeśli chcesz konkretny adres, można podać: Wawel 5, 31-001 Kraków, Polska.
==========
```
  
Widzimy, że każdy z tych odpowiedzi zawiera różne informacje. Nie są one ze sobą sprzeczne, jednak ... rożne obszary/zakresy.

Wyobraźmy sobie, że chodzi nam w tym pytaniu o konkretny adres, gdzie Smoka Wawelskiego znajdziemy.
  
## Doprecyzowanie rodzaju informacji, którą chcemy uzyskać

```Python
response = client.chat.completions.create(
    model = "gpt-4o-mini-2024-07-18",
    messages = [
        {"role": "user", "content": "Gdzie mieszka Smok Wawelski? Zwróć adres."}
    ],
    n = 5
)

for ch in response.choices:
    print(ch.message.content)
    print("=" * 10)
```

```Python
Smok Wawelski mieszka na Wawelu, w Krakowie, w Polsce. Jego legendarna jaskinia znajduje się pod Wzgórzem Wawelskim, w pobliżu zamku królewskiego. W rzeczywistości nie ma konkretnego adresu dla smoka, ponieważ jest to postać z legendy, ale ogólnie można powiedzieć, że jego „dom” to Zamek Wawelski, Wawel 5, 31-001 Kraków, Polska.
==========
Smok Wawelski mieszka w Krakowie, pod Wawelem, przy Wawel 5. To miejsce jest znane z legendy o Smoku Wawelskim, który według tradycji zamieszkiwał jaskinię pod Wzgórzem Wawelskim.
==========
Smok Wawelski mieszka na Wawelu, w Krakowie, w Polsce. Jego legendarne miejsce zamieszkania to jaskinia w pobliżu Wawelskiego Zamku, w okolicach brzegu Wisły. Dokładny adres to: Wawel 5, 31-001 Kraków, Polska.
==========
Smok Wawelski mieszka w Krakowie, a jego legenda związana jest z Wzgórzem Wawelskim. Wszyscy znają go jako mieszkańca pod Wawelskim zamkiem, który według legendy miał terroryzować okoliczne tereny. Adres, który można podać, to: Wawel, 31-001 Kraków, Polska.
==========
Smok Wawelski mieszka w Krakowie, a jego legendarna siedziba znajduje się pod Wzgórzem Wawelskim, na którym stoi Zamek Królewski na Wawelu. Jeśli chcesz konkretny adres, można podać: Wawel 5, 31-001 Kraków, Polska.
==========
```

Mimo doprecyzowania prośby o zwróceniu adresu, ponownie każda odpowiedź wygląda inaczej, nie wszystkie zawierają pełny adres razem z kodem pocztowym, który może być nam potrzebny.

## Określmy teraz te informacje jeszcze precyzyjniej
  
```Python
prompt = """
Gdzie mieszka Smok Wawelski? Zwróć tylko te informacje:
- ulica
- miejscowość
- województwo
- kod pocztowy
"""

response = client.chat.completions.create(
    model = "gpt-4o-mini-2024-07-18",
    messages = [
        {"role": "user", "content": prompt}
    ],
    n = 5
)

for ch in response.choices:
    print(ch.message.content)
    print("=" * 10)
```

```Python
Smok Wawelski mieszka w:

- ulica: Wawel
- miejscowość: Kraków
- województwo: małopolskie
- kod pocztowy: 31-001
==========
Smok Wawelski mieszka w:

- ulica: Wawel
- miejscowość: Kraków
- województwo: małopolskie
- kod pocztowy: 31-001
==========
Smok Wawelski mieszka w:

- ulica: Wawel 5
- miejscowość: Kraków
- województwo: małopolskie
- kod pocztowy: 31-001
==========
Smok Wawelski mieszka w:

- ulica: Wawel
- miejscowość: Kraków
- województwo: małopolskie
- kod pocztowy: 31-001
==========
Smok Wawelski mieszka w:

- ulica: Wawel
- miejscowość: Kraków
- województwo: małopolskie
- kod pocztowy: 31-001
==========
```
  
Teraz informacje mają już konkretną strukturę, jednak zwróćmy uwagę, że jedna z odpowiedzi w wierszu "ulica" zawiera numer budynku, reszta samą nazwę ulicy. Co jednak jeśli potrzebowalibyśmy odwołać się do konkretnej informacji z tego outputu, chcąc ją gdzieś wykorzystać? Możemy poprosić LLM'a o zwrócenie odpowiedzi w formacie JSON.

## Output w formacie JSON

Dopisujemy do naszego prompta jedynie zdanie "Zwróć to jako JSON."

```Python
prompt = """
Gdzie mieszka Smok Wawelski? Zwróć tylko te informacje:
– ulica
– miejscowość
– województwo
– kod pocztowy

Zwróć to jako JSON.
"""

response = client.chat.completions.create(
    model = "gpt-4o-mini-2024-07-18",
    messages = [
        {"role": "user", "content": prompt}
    ],
    n = 5
)

for ch in response.choices:
    print(ch.message.content)
    print("=" * 10)
```
    
```Python
```json
{
  "ulica": "Wawel",
  "miejscowość": "Kraków",
  "województwo": "Małopolskie",
  "kod_pocztowy": "31-001"
}
```
==========
```json
{
  "ulica": "Wawel",
  "miejscowość": "Kraków",
  "województwo": "małopolskie",
  "kod_pocztowy": "31-001"
}
```
==========
```json
{
  "ulica": "Wawel",
  "miejscowość": "Kraków",
  "województwo": "małopolskie",
  "kod_pocztowy": "31-001"
}
```
==========
```json
{
  "ulica": "Wawel",
  "miejscowość": "Kraków",
  "województwo": "Małopolskie",
  "kod pocztowy": "31-001"
}
```
==========
```json
{
  "ulica": "Wawel",
  "miejscowość": "Kraków",
  "województwo": "Małopolskie",
  "kod_pocztowy": "31-001"
}
```
==========
```

```Python
```

```Python
```

```Python
```

```Python
```

```Python
```
