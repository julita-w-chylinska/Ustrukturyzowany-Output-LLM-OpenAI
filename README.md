# Structured-LLM-OpenAI

Duże modele językowe (Large Language Models, LLM) mają to do siebie, że przy użyciu tych samych promptów generują różne odpowiedzi, nie tylko pomiędzy różnymi modelami, ale też w obrębie tego samego. 

Jest to przeszkodą, jeśli chcemy za każdym razem otrzymywać odpowiedzi o takiej samej strukturze. Dodatkowo LLM’y mogą generować różną ilość tekstu i podawać informacje, których nie potrzebujemy. Z pomocą przychodzi tu API konkretnych modeli LLM'ów i dostępne w nim mechanizmy Function Calling i Structured Output. 

Ten projekt ma postać testowania możliwości tych funkcjonalności i dotyczy API OpenAI, modelu gpt-4o-mini-2024-07-18. W tym repozytorium będę poługiwać się językiem polskim ze względu na używanie tego języka do promptów w tym projekcie.


## Pięć niezbędnych kroków do rozpoczęcia pracy z API OpenAI

1. Utworzenie płatnego klucza API na platformie OpenAI

Link:
https://platform.openai.com/api-keys

3. Instalacja pakietu openai w środowisku Pythona

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

5. Inicjalizacja klienta OpenAI

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

Odpowiedź:
  
```text
Działa, wszystko w porządku!
```
## Jak na pytanie "Gdzie mieszka Smok Wawelski?" odpowiedzą modele z różnych platform?
  
### ChatGPT
  
<img width="500" alt="image" src="https://github.com/user-attachments/assets/aff8ef7d-2f6c-4c67-a50f-45f3a486fbec" />
  
### Perpexity
  
<img width="500" alt="image" src="https://github.com/user-attachments/assets/a32b35f0-af01-45e0-afa9-b0e4e3a38ad1" />
  
### DeepSeek
  
<img width="500" alt="image" src="https://github.com/user-attachments/assets/6ca11826-c42f-4437-960a-09e25d6970bc" />
  
## Jak na to pytanie odpowiedziałby LLM z użyciem API?

```Python
response = client.chat.completions.create(
    model = "gpt-4o-mini-2024-07-18",
    messages = [
        {"role": "user", "content": "Gdzie mieszka Smok Wawelski?"}
    ]
)

print(response.choices[0].message.content)
```

Odpowiedź:
  
```text
Smok Wawelski, legenda związana z Krakowem, mieszkał w jaskini znajdującej się pod Wawelem, na wzgórzu Wawelskim. Jaskinia, znana jako "Smokowa Jama", jest teraz popularną atrakcją turystyczną. W legendach smok terroryzował mieszkańców Krakowa, aż został pokonany przez księcia Kraka, co zakończyło jego rządy terroru. Współczesny Kraków często wspomina tę legendę, a także można tam znaleźć pomnik Smoka Wawelskiego, który czasami "zionie" ogniem.
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
  
Odpowiedź:
  
```text
Smok Wawelski, postać z polskich legend, według tradycji mieszkał w jaskini pod Wawelem w Krakowie, nad rzeką Wisłą. Jaskinia ta nazywana jest Smoczą Jamą. Legenda głosi, że smok terroryzował mieszkańców miasta, aż został pokonany przez dzielnego rycerza, co przyczyniło się do upadku jego panowania nad Krakowem. Smok Wawelski stał się symbolem miasta i przyciąga turystów z całego świata.
==========
Smok Wawelski, legendarny smok krakowski, według polskiej legendy mieszkał w jaskini pod Wawelem, w pobliżu Zamku Królewskiego na Wawelu w Krakowie. Jaskinia ta, znana jako Jaskinia Smoka Wawelskiego, jest popularnym miejscem turystycznym i znajduje się u podnóża wzgórza wawelskiego, nad Wisłą. Według legendy smok terroryzował mieszkańców Krakowa, aż został pokonany przez dzielnego rycerza – Szewczyka Dratewkę.
==========
Smok Wawelski mieszka w grocie na Wawelu, która znajduje się u podnóża Wawelu w Krakowie. Legenda głosi, że smok terroryzował mieszkańców miasta, aż został pokonany przez dzielnego rycerza, według niektórych wersji historycznych - przez Krakusa. Grota smoka jest popularnym miejscem turystycznym, a przy wyjściu z niej znajduje się pomnik Smoka Wawelskiego, który zionie ogniem.
==========
Smok Wawelski mieszka w jaskini pod Wzgórzem Wawelskim w Krakowie, w Polsce. Jest to popularna postać z polskiej legendy, według której smok terroryzował mieszkańców Krakowa, aż został pokonany przez dzielnego rycerza, w zależności od wersji opowieści, zazwyczaj jest to Krakus lub Szewczyk Dratewka. Jaskinia smoka, znana jako Smocza Jama, jest dziś turystyczną atrakcją, którą można zwiedzać.
==========
Smok Wawelski to legendarna postać związana z Krakowem, a według legendy mieszkał w jaskini u podnóża Wawelu, w tzw. Jaskini Smoczej. Jaskinia ta znajduje się na Wawelu, przy zamku królewskim. To właśnie tam według legendy smok terroryzował mieszkańców, aż został pokonany przez dzielnego rycerza, który podjął się wyzwania, by go zlikwidować. Dziś Jaskinia Smocza jest popularnym miejscem turystycznym w Krakowie.
==========
```
  
Widzimy, że każdy z tych odpowiedzi zawiera różne informacje. Nie są one ze sobą sprzeczne, jednak pokrywają różne obszary dotyczące tematu miejsca przebywania czy też samej legendy smoka.

Wyobraźmy sobie, że w pytaniu o miejsce zamieszkania smoka, chodzi nam o konkretny adres.
  
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

```text
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

```text
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

- ulica: Wawel
- miejscowość: Kraków
- województwo: małopolskie
- kod pocztowy: 31-001
==========
```
  
Teraz informacje mają już konkretną strukturę i odpowiedzi są stabilne. Co jednak jeśli potrzebowalibyśmy odwołać się do konkretnej informacji z tego outputu, chcąc ją gdzieś wykorzystać? Możemy poprosić LLM'a o zwrócenie odpowiedzi w formacie JSON.

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
    
````text
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
````

Gdyby teraz dodatkowo sparsować tę odpowiedź do słownika:

```Python
import json

parsed_json = json.loads(
    response.choices[0].message.content.replace("json", "").replace("```", "")
)
```
    
...pozwoliłoby to nam sięgnąć do konkretnej jej części, na przykład zapisując informację o miejscowości do zmiennej:

```Python
smok_miejscowosc = parsed_json["miejscowość"]
```

Sprawdźmy, co zapisało się pod zmienną `smok_miejscowosc`:

```Python
print(smok_miejscowosc)
```
```text
Kraków
```

Wykorzystując format JSON możemy również przekształcić odpowiedź LLM'a na ładny format:
  
```Python
parsed_json = json.loads(
    response.choices[0].message.content.replace("json", "").replace("```", "").replace("_", " ")
)

for k, v in parsed_json.items():
    print(f"{k}: {v}")
```

```text
ulica: Smok Wawelski
miejscowość: Kraków
województwo: Małopolskie
kod pocztowy: 31-001
```
  
Jednak czy istnieje jakieś natywne wsparcie, które zagwarantuje poprawne wyświetlanie ustruktyrozowanego outputu w JSON'ie (również przy tych bardziej rozbudowanych)? Z pomocą przychdodzi tu mechanizm Function Calling.

## Function Calling

Na przykładzie sytuacji z poszukiwaniem przepisu na naleśniki wypróbujemy – poprzez parametr `functions` – przekazanie LLM'owi schematu, którego ma się trzymać podczas generowania odpowiedzi. Dodatkowo użyjemy wiadomości o roli `system` w parametrze `messages`, aby zdefiniować globalną instrukcję zachowania modelu (tzw. system prompt). Taka instrukcja działa jak nadrzędny kontekst dla całej rozmowy: narzuca, że asystent ma generować przepisy oraz preferować format JSON.

```Python
schema = {
    "type": "object",
    "properties": {
        "dish": {"type": "string"},
        "ingredients": {"type": "array", "items": {"type": "string"}},
        "steps": {"type": "array", "items": {"type": "string"}}
    },
    "required": ["dish", "ingredients", "steps"]
}

function = [{"name": "Dish", "parameters": schema}]

messages = [
    {"role": "system", "content": "Jesteś pomocnym asystentem, który generuje przepisy w formacie JSON."},
    {"role": "user", "content": "Przygotuj naleśniki"}
]

response = client.chat.completions.create(
    model = "gpt-4o-mini-2024-07-18",
    messages = messages,
    max_tokens = 1000,
    functions = [{"name": "Dish", "parameters": schema}],
    function_call = "auto"
)
```

W ten sposób model zwraca wywołanie funkcji zamiast tekstu. Do samej odpowiedzi możemy dostać się wtedy w poniższy sposób (odwołując się do argumentu tej funkcji):
  
```Python
print(response.choices[0].message.function_call.arguments)
```

```text
{"dish":"Naleśniki","ingredients":["1 szklanka mąki pszennej","2 jajka","1 i 1/2 szklanki mleka","1/4 szklanki wody gazowanej","szczypta soli","olej do smażenia","cukier (opcjonalnie)"],"steps":["W dużej misce wymieszaj mąkę i sól.","Dodaj jajka, mleko oraz wodę gazowaną i dokładnie wymieszaj, aż ciasto będzie gładkie.","Odstaw ciasto na około 30 minut, aby odpoczęło.","Rozgrzej patelnię z odrobiną oleju na średnim ogniu.","Wlej chochelkę ciasta na patelnię, a następnie przechyl ją, aby ciasto równomiernie pokryło dno patelni.","Smaż przez około 1-2 minuty, aż brzegi naleśnika będą lekko złote, a następnie obróć i smaż jeszcze przez 1-2 minuty z drugiej strony.","Powtarzaj, aż całe ciasto zostanie zużyte.","Podawaj naleśniki z ulubionymi dodatkami, takimi jak dżem, owoce czy bita śmietana."]}
```

Parsując JSON'a w ten sposób:

```Python
args = json.loads(response.choices[0].message.function_call.arguments)
print(json.dumps(args, indent=2, ensure_ascii=False))
```

... otrzymamy czytelny przepis:

```text
{
  "dish": "Naleśniki",
  "ingredients": [
    "1 szklanka mąki pszennej",
    "2 jajka",
    "1 i 1/2 szklanki mleka",
    "1/4 szklanki wody gazowanej",
    "szczypta soli",
    "olej do smażenia",
    "cukier (opcjonalnie)"
  ],
  "steps": [
    "W dużej misce wymieszaj mąkę i sól.",
    "Dodaj jajka, mleko oraz wodę gazowaną i dokładnie wymieszaj, aż ciasto będzie gładkie.",
    "Odstaw ciasto na około 30 minut, aby odpoczęło.",
    "Rozgrzej patelnię z odrobiną oleju na średnim ogniu.",
    "Wlej chochelkę ciasta na patelnię, a następnie przechyl ją, aby ciasto równomiernie pokryło dno patelni.",
    "Smaż przez około 1-2 minuty, aż brzegi naleśnika będą lekko złote, a następnie obróć i smaż jeszcze przez 1-2 minuty z drugiej strony.",
    "Powtarzaj, aż całe ciasto zostanie zużyte.",
    "Podawaj naleśniki z ulubionymi dodatkami, takimi jak dżem, owoce czy bita śmietana."
  ]
}
```

Dzięki Function Calling dostajemy output w sposób bardziej przewidywalny. Nie jest to jednak wystarczające, ponieważ nawet jeśli mamy JSON na wyjściu, nadal nie jest to ten poziom kontroli, który chcielibyśmy i moglibyśmy mieć. W tym celu dochodzimy do kolejnej warstwy, która może nas wesprzeć, czyli Structured Output wraz z biblioteką Pydantic.

## Structured Output + Pydantic

W celu ... należy zainstalować i zaimportować [...]
  
```Python
pip install instructor
```
  
```Python
from pydantic import BaseModel
import instructor
```
  
```Python
client = instructor.from_openai(OpenAI())
```

Wróćmy do przykładu z adresem Smoka Wawelskiego.
  
Poprzez stworzenie klasy `Address` z obiektami określającymi informacje, które chcemy otrzymać od LLM'a, a następnie przekazanie tej klasy do parametru `response_model`:
  
```Python
class Address(BaseModel):
    street: str
    city: str
    state: str
    postal_code: str

response = client.chat.completions.create(
    model = "gpt-4o-mini-2024-07-18",
    messages = [
        {"role": "user", "content": "Gdzie mieszka Smok Wawelski?"}
    ],
    response_model = Address
)
```

... możemy w prosty sposób dotrzeć do informacji o samym kodzie pocztowym:

```Python
print(response.postal_code)
```

```text
31-001
```

Dodatkowo, jeśli poszczególne części odpowiedzi LLM'a miałaby spełnić jakieś konkretne wymogi, możemy je dopisać za pomocą obiektu Field z biblioteki Pydantic. W poniższym przykładzie narzucamy format na informację o ulicy, miejscowości/mieście i województwie/stanie:

```Python
class Address(BaseModel):
    street: str = Field(..., description="Ulica, numer domu/bloku i ewentualnie mieszkania.")
    city: str = Field(..., description="Nazwa miejscowości – wszystkie litery muszą być wielkie, np. WARSZAWA.")
    state: str = Field(..., description="Nazwa województwa – tylko pierwsza litera duża, np. Mazowieckie.")
    postal_code: str

response = client.chat.completions.create(
    model = "gpt-4o-mini-2024-07-18",
    messages = [
        {"role": "user", "content": "Gdzie mieszka Smok Wawelski?"}
    ],
    response_model = Address
)

response.street, response.city, response.state, response.postal_code
```

... w wyniku czego otrzymamy poniższą, zgodną z wymogami odpowiedź:
  
```text
('Wawel 5', 'KRAKÓW', 'Małopolskie', '31-001')
```

---

## Autor: Julita Wawreszuk-Chylińska (w oparciu o tutorial DataWorkshop)

**LinkedIn**: [Julita Wawreszuk-Chylińska](http://www.linkedin.com/in/julita-w-chylinska)

Dziękuję za Twoje zainteresowanie tym projektem!
## Author: Julita Wawreszuk-Chylińska

**LinkedIn**: [Julita Wawreszuk-Chylińska](http://www.linkedin.com/in/julita-w-chylinska)

Thank you for your interest in this project!
