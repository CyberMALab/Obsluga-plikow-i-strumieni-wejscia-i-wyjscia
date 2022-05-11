# Obsługa plików i strumieni wejścia i wyjścia
## Strumienie
W języku ANSI C  istnieją 3 standardowe strumienie:

- Strumień wyjścia – stdout
- Strumień wejścia – stdin
- Strumień błędów - stderr 

Strumień wyjścia i strumień błędów jest ustawiony standardowo na ekran konsoli, a strumień wejścia również na konsolę – tylko na odczyt. Funkcje printf i scanf korzystają właśnie ze standardowego strumienia wyjścia i wejścia, więc komunikację prowadzą przez ekran konsoli. Standardowe strumienie można przekierować na inne źródła wejścia i wyjścia, jednak używa się tego rzadko. Zdecydowanie częściej używa się  funkcji obsługujących strumienie wejścia i wyjścia, dające kontrolę co do używanego strumienia. Funkcje zostały zestawione w tabeli poniżej. 

|Funkcja z argumentami|działanie|
| - | - |
|`int fprintf(FILE* c, const char* format,…)`|Analogiczne działanie do `printf`, tylko za argument `FILE* c` przyjmuje strumień wyjścia.|
|`int fscanf(FILE* c, const char* format,…)`|Analogiczne działanie do `scanf`, tylko za argument `FILE* c` przyjmuje strumień wyjścia. |
|`char* fgets(char* str, int size, FILE* c)`|Funkcja pobiera napis do bufora `str`, ale maksymalnie o rozmiarz `size`, ze strumienia `c`. Zwraca wskaźnik do pobranego napisu.|
|`int fputs(const char* str, FILE* c)`|Funkcja zapisuje `str` do strumienia `c`.|

*Przykład (15.0) użycie funkcji korzystających jawnie ze strumieni wejścia i wyjścia*

```
#include <stdio.h>
#include <stdlib.h>

int main()
{
	char fromStdin[20];
	
	fprintf(stdout, "Tu jest stdout\n");
	fprintf(stderr, "Tu jest stderr\n");
	
	/* tutaj pobierze napis do pierwszej spacji*/
	fscanf(stdin, " %s", fromStdin);
	
	fprintf(stdout, "%s\n", fromStdin);
	
	/* czyszczenie bufora stdin*/
	fflush(stdin);
	/* tutaj pobierze napis do pierwszego końca lini*/
	fgets(fromStdin, 20, stdin);
	
	fputs(fromStdin, stdout);
	
	
	return 0;
}

```

*Wynik wykonania programu*

>Tu jest stdout
>
>Tu jest stderr
>
>stdin by fscanf
>
>stdin
>
>stdin by fgets
>
>stdin by fgets


## Pliki

W programowaniu istnieje rozróżnienie na pliki tekstowe i pliki binarne. Koncepty te są intuicyjne i sprowadzić można je do następujących nieformalnych definicji:

Pliki tekstowe – są czytelne dla człowieka (można je otworzyć w dowolnym edytorze tekstu) i z tego powodu zwykle wymagają szeregu konwersji przed wykonaniem zasadniczych operacji na danych w nich zawartych w programie, który je przetwarza.

Pliki binarne – są nieczytelny dla człowieka, bardzo często dane przechowywane są w sposób identyczny jak ma to miejsce w pamięci operacyjnej dzięki czemu zapis i odczyt jest bardzo szybki. Wykonywanie „ręcznych” modyfikacji pliku jest znacząco utrudnione i wymaga pewnej wiedzy oraz specjalistycznych narzędzi programowych.

## Wskaźniki do plików, funkcje obsługujące pliki

W języku ANSI C wskaźnik do pliku, ma specjalny typ zmiennej, znajdujący się w bibliotece stdio.h – `FILE`. Zmienna ta jest zmienną złożoną, zawierającą w sobie informacje takie jak rozmiar pliku w bajtach, rodzaj dostępu do pliku (odczyt, zapis, aktualizacja), znacznik pozycji w pliku. Wszystkie operacje które wykonywać będziemy na pliku, będą wymagały operowania na zmiennej wskaźnikowej do pliku. Zmienna wskaźnikowa do otwartego pliku, jest strumieniem, wejścia lub wyjścia, albo i wejścia i wyjścia , w zależności od trybu otwarcia pliku, więc do ich obsługi używa się funkcji, które zezwalają na wybór strumienia do obsługi.

Otwarcie pliku można wykonać wykorzystując funkcję fopen. Funkcja ta przyjmuje tablicę znaków – ścieżka dostępu do pliku, oraz kolejny łańcuch znaków określający tryb otwarcia pliku. Funkcja zwraca wartość wskaźnika typu FILE, lub NULL w przypadku nieudanej próby otwarcia.

Tryby otwarcia plików zestawione zostały w poniższej tabeli.

|**argument funkcji**|**tryb otwarcia pliku**|**uwagi**|
| :-: | - | :-: |
|„r” lub „rt”|Odczyt tekstowy|Jeśli plik nie istnieje funkcja `fpoen` zwraca `NULL`|
|“rb”|Odczyt binarny|Jeśli plik nie istnieje funkcja `fpoen` zwraca `NULL`|
|r+” lub “r+t”|Aktualizacja tekstowa (odczyt i zapis)|Jeśli plik nie istnieje funkcja `fpoen` zwraca `NULL`|
|“r+b”|Aktualizacja binarna (odczyt i zapis)|Jeśli plik nie istnieje funkcja `fpoen` zwraca `NULL`|
|“w” lub “wt”|Zapis tekstowy|Jeśli plik nie istnieje jest nadpisywany nową zawartością. Jeśli nie, jest tworzony|
|“wb”|Zapis binarny|Jeśli plik nie istnieje jest nadpisywany nową zawartością. Jeśli nie, jest tworzony|
|“w+” lub “w+t”|Aktualizacja tekstowa (odczyt i zapis)|Jeśli plik nie istnieje jest nadpisywany nową zawartością. Jeśli nie, jest tworzony|
|“w+b”|Aktualizacja binarna (odczyt i zapis)|Jeśli plik nie istnieje jest nadpisywany nową zawartością. Jeśli nie, jest tworzony|
|“a” lub “at”|Dopisywanie tekstowe na końcu|Jeśli plik istnieje, dane są dopisywane na jego końcu, jeśli nie istnieje jest tworzony|
|“ab”|Dopisywanie binarne na końcu|Jeśli plik istnieje, dane są dopisywane na jego końcu, jeśli nie istnieje jest tworzony|
|“a+” lub “a+t”|Aktualizacja tekstowa (odczyt i zapis) na końcu|Jeśli plik istnieje, dane są dopisywane na jego końcu, jeśli nie istnieje jest tworzony|
|“a+b”|Aktualizacja binarna (odczyt i zapis) na końcu|Jeśli plik istnieje, dane są dopisywane na jego końcu, jeśli nie istnieje jest tworzony|

Istotnym elementem jest również zamknięcie pliku. Taką usługę oferuje nam funkcja fclose, która za argument przyjmuje wskaźnik na otwarty plik typu FILE. 

*Przykład (15.1) ogólny zarys programu obsługującego plik*

```
#include <stdio.h>
#include <stdlib.h>

int main()
{
	FILE *fp;
	/* otwarcie pliku */
	fp = fopen("plik.txt", "r+");
	
	/*obsługa błędu otwarcia */
	if (fp == NULL)
	{
		fprintf(stderr, "Blad otwarcia pliku!");
		return 1;
	}
	
	/* ciało programu 
	...
	*/
	
	/* zamknięcie pliku */
	fclose(fp);
	return 0;
}
```
Zapis i odczyt z plików tekstowych, wykonuje się poprzez użycie funkcji wejścia/wyjścia dających wybór strumienia zapisu i odczytu. Do odczytu i zapisu binarnego istnieją specjalne funkcje:

`fwrite` - Funkcja służy do zapisu binarnego. Wysyła do wskazanego strumienia n elementów (każdy o rozmiarze `size` bajtów) pobranych z obszaru pamięci wskazywanego przez `ptr`:

- `size_t fwrite (const void* ptr, size_t size, size_t n, FILE* fp);`

`fread` – Funkcja służy do odczytu binarnego. Odczytuje ze wskazanego strumienia n elementów (każdy o rozmiarze `size` bajtów) i zapisuje w obszarze pamięci wskazywanym przez `ptr`:

- `size_t fread (void* ptr, size_t size, size_t n, FILE* fp);`

Język C udostępnia nam również narzędzia służące do poruszania się po pliku. Posługują się one znacznikiem pozycji strumienia (zawartego wewnątrz zmienne FILE). Znacznik taki określa miejsce (znak lub bajt), w którym nastąpi bieżąca operacja odczytu lub zapisu. Znacznik taki można odczytać, oraz ustawić na żądaną wartość za pomocą poniższych funkcji:

|Funkcja|Działanie|
| :- | :- |
|`long ftell (FILE* fp);`|Zwraca aktualną wartość znacznika.|
|`int fseek (FILE* fp, long ofs, int origin);`|Ustawia znacznik pliku gdzie orgin przyjmuje wartości wyliczeniowe: -`SEEK_SET` – ofs jest liczony od początku pliku, -`SEEK_CUR` – ofs jest liczony od bieżącej pozycji pliku, -`SEEK_END` – ofs jest liczony od końca pliku.|
|`void rewind (FILE* fp);`|Ustawia pozycję znacznika na początek pliku.|
|`int fgetpos (FILE* fp, fpos_t* ptr);`|Zapisuje obecną wartość znacznika do *ptr.|
|`int fsetpos (FILE* fp, const fpos_t* ptr);`|Ustawienie pozycji znacznika na znacznik podany w *ptr.|
|`int feof (FILE* fp);`|Wykrywa znak końca pliku EOF (zwraca wartość różną od 0 jeśli osiągnięto koniec pliku).|
|`int remove  (const char* fname);`|Usuwa plik o podanej nazwie (ścieżce).|
|`int rename(const char* oldName, const char* newName);`|Zmienia nazwę pliku.|
|`FILE* tmpfile(void);`|Tworzy plik tymczasowy w trybie ”w+b” .|
|`char* tmpnam(char s[L_tmpnam]);`|Generuje unikalną nazwę piku.|

Przykłady z prezentacji - dopisać

*Przykład (15.2) dopisanie na końcu pliku*

```
#include <stdio.h>

int main()
{
	FILE* fp;
	char tekst[]= "Hello world\n";
	
	if((fp=fopen("test.txt","a"))==NULL)
	{
		return 1;
	}
	
	fclose(fp);
	return 0;
}

```

*Wynik działania programu – pierwsze uruchomienie:*

*Został stworzony plik text.txt z zawartością:*

> Hello world

*Wynik działania programu – drugie uruchomienie:*

> Hello world
>
> Hello world

## Zadania do samodzielnego wykonania:

*WKRÓTCE*


***
[Poprzednia część](https://github.com/CyberMALab/Dynamiczna-alokacja-pamieci.git) | [Spis treści](https://github.com/CyberMALab/Wprowadzenie-do-programowania-w-j-zyku-ANSI-C.git) | [Następna część](https://github.com/CyberMALab/Comming-Soon.git)
***
&copy; Cyberspace Modelling and Analysis Laboratory

