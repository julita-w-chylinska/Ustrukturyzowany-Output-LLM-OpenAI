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
<br>
<img width="900" alt="image" src="https://github.com/user-attachments/assets/6ca11826-c42f-4437-960a-09e25d6970bc" />

<br> <br> <br>

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

```Python
```

```Python
```

```Python
```

```Python
```
