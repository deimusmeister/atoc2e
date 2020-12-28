# 3 Մոդուլյարություն

_Մի՛ ընդհատեք ինձ, քանի դեռ ե՛ս եմ ընդհատում։_

_— Ուինսթոն Ս. Չերչիլ_


* Ներածություն
* Անջատ կոմպիլյացիա
* Մոդուլներ
* Անունների տիրույթներ
* Սխալների մշակում
    Բացառություններ; Ինվարիանտներ; Սխալների մշակման այլընտրանքներ; Կոնտրակտներ; Ստատիկ պնդումներ
* Ֆունկցիայի արգումենտներ և վերադարձրած արժեքներ
    Արգումենտների փոխանցում; Վերադարձրած արժեք; Structured binding
* Խորհուրդներ


## 3.1 Ներածություն

C++ լեզվով գրված ծրագիրը բաղկացած է բազմաթիվ առանձին մշակված մասերից, ինչպիսիք են ֆունկցիաները (§1.2.1), օգտագործողի սահմանած տիպերը ([Գլուխ 2](ch-02-user-defined-types.md)), դասերի հիերարխիան (§4.5) և կաղապարները ([Գլուխ 6](ch-06-templates.md))։ Այս ամենը հաջող կառավարելու համար կարևոր է հստակ սահմանել բոլոր մասերի փոխհարաբերությունները։ Առաջին և ամենակարևոր քայլը որևէ բաղադրիչի ինտերֆեյսի և դրա իրականացման հստակ տարանջատումն է։ Լեզվի մակարդակում C++-ը ինտերֆեյսերը ներկայացնում է հայտարարությունների միջոցով։ _Հայտարարությամբ_ որոշվում է այն ամենը, ինչ հարկավոր է ֆունկցիան կամ տիպն օգտագործելու համար։ Օրինակ․

```c++
double sqrt(double);  // քառակուսի արմատի ֆունկցիան ստանում է double
                      // արժեք և վերադարձնում է double արժեք

class Vector {
    public:
        Vector(int s);
        double& operator[] (int i);
        int size();

    private:
        double* elem;      // elem֊ը ցույց է տալիս sz քանակի double արժեքներով զանգվածի վրա
        int sz;
};
```

Այստեղ էական է այն, որ ֆունկցիաների մարմինները՝ դրանց _սահմանումները_, գտնվում են «ինչ-որ այլ տեղ»։ Այս օրինակի համար թերևս կուզենայինք, որ `Vector`֊ի ներկայացումը նույնպես լիներ «մի ուրիշ տեղ», բայց այդ հարցը կքննարկենք քիչ ավելի ուշ (վերացական տիպեր; §4.3)։ `sqrt()`-ի սահմանումը կունենա հետևյալ տեսքը․

```c++
double sqrt(double d)         // sqrt()֊ի սահմանումը
{
    // ․․․մաթեմատիկայի դասագրքում գրված ալգորիթմը․․․
}
```

`Vector`֊ի համար պետք է սահմանենք բոլոր երեք ֆունկցիա֊անդամները․

```c++
Vector::Vector(int s)            // կոնստրուկտորի սահմանումը
    :elem{new double[s]}, sz{s}   // արժեքավորել անդամները
{
}

double& Vector::operator[] (int i)  // ինդեքսի օպերատորի սահմանումը
{
    return elem[i];
}

int Vector::size()                  // size()֊ի սահմանումը
{
    return sz;
}
```

Մենք պետք է սահմանենք `Vector`-ի ֆունկցիաները, բայց ոչ `sqrt()` ֆունկցիան, քանի որ այն ստանդարտ գրադարանի մաս է։ Այնուամենայնիվ, դա ոչ մի էական նշանակություն չունի. գրադարանը պարզապես «մի կոդ է, որն օգտագործում ենք»՝ իրականացված լեզվի նույն գործիքներով, որոնք մենք ենք օգտագործում։

Որևէ էության համար, ինչպիսին ֆունկցիան է, կարող են լինել շատ հայտարարություններ, բայց միայն մեկ սահմանում։


## 3.2 Անջատ կոմպիլյացիա

C++ լեզուն ապահովում է անջատ կոմպիլյացիայի հնարավորությունը, երբ թարգմանվող կոդը տեսնում է միայն օգտագործվող տիպերի և ֆունկցիաների հայտարարությունները։ Այդ տիպերի և ֆունկցիաների սահմանումները գտնվում են այլ ֆայլերում և թարգմանվում են առանձին։ Այս հնարավորությունը կարող է օգտագործվել ծրագիրը որպես կիսաանկախ կոդի հատվածների բազմություն կազմակերպելու համար։ Այսպիսի առանձնացումը կարող է օգտագործվել կոմպիլյացիայի ժամանակը նվազեցնելու, ինչպես նաև ծրագրի տրամաբանորեն անկախ մասերը հստակ առանձանցնելու համար (դրանով իսկ նվազեցնելով սխալների հավանականությունը)։ Գրադարանը հաճախ առանձին թարգմանված կոդերի հատվածների (օր․ ֆունցիաների) հավաքածու է։

Սովորաբար մոդուլի ինտերֆեյսը որոշող հայտարարությունները գրառում ենք մի ֆայլում, որի անունն արտացոլում է կիրառության նպատակը։ Օրինակ․

```C++
// Vector.h:

class Vector {
public:
    Vector(int s);
    double& operator[] (int i);
    int size();
private:
    double* elem;     // elem֊ը ցույց է տալիս double տիպի sz տարրերի զանգվածի վրա
    int sz;
};
```

Այս հայտարարությունը գրառվում է `Vector.h` ֆայլում։ Այնուհետև, `Vector`-ի ինտերֆեյսը հասանելի դարձնելու համար, օգտագործողները _կցում_ են (include) այդ _վերնագրային ֆայլը_ (header file)։ Օրինակ․

```C++
// user.cpp:

#include "Vector.h"  // ստանալ Vector֊ի ինտերֆեյսը
#include <cmath>     // ստանալ ստանդարտ գրադարանի մաթեմատիկական
                     // ֆունկցիաների ինտերֆեյսը՝ ներառյալ sqrt() ֆունկցիայինը

double sqrt_sum(Vector& v)
{
    double sum = 0;
    for (int i = 0; i != v.size(); ++i)
        sum += sqrt(v[i]);   // քառակուսի արմատների գումարը
    return sum;
}
```

Կոմպիլյատորի աշխատանքի համաձայնեցվածությունն ապահովելու համար `Vector`֊ի իրականացումը պարունակող `.cpp` ֆայլում նույնպես կցվում է ինտերֆեյսը նկարագրող `.h` ֆայլը․

```C++
// Vector.cpp:

#include "Vector.h"   // կցում ենք ինտերֆեյսը

Vector::Vector (int s)
    :elem {new double[s]}, sz{s}       // ինիցիալիզացնում ենք անդամները
{
}

double& Vector::operator[] (int i)
{
    return elem[i];
}

int Vector::size()
{
    return sz;
}
```

`user.cpp` և `Vector.cpp` ֆայլերի կոդերը համատեղ օգտագործում են `Vector.h`-ում ներկայացված ինտերֆեյսը, բայց այլ տեսակետից այդ երկու ֆայլերն իրարից լիովին անկախ են և կարող են առանձին թարգմանվել։ Գրաֆիկորեն ծրագրի հատվածները կարող են ներկայացվել այսպես․

![](images/image-008.jpeg)

Ճիշտն ասած, անջատ կոմպիլյացիայի կիրառությունը լեզվի խնդիր չէ. դրա նպատակը լեզվի կոնկրետ իրականացման հնարավորություններն ավելի լավ օգտագործելն է։ Այնուամենայնիվ այն ունի մեծ գործնական նշանակություն։ Ծրագրի կազմակերպման լավագույն մոտեցումը այն որպես ճիշտ սահմանված (well-defined) կախվածություններով մոդուլների բազմություն պատկերացնելն է՝ այդ մոդուլյարությունը տրամաբանորեն ներկայացնելով լեզվի հնարավորություններով, ապա նույն այդ մոդուլյարությունը ֆիզիկապես ներկայացնել ֆայլերով՝ արդյունավետ անջատ կոմպիլյացիայի համար։ ??

Ինքնուրույն թարգմանվող `.cpp` ֆայլը (ներառյալ կցված վերնագրային ֆայլերը) կոչվում է _թարգմանության միավոր_ (translation unit)։ Ծրագիրը կարող է բաղկացած լինել հազարավոր թարգմանության միավորներից։


## 3.3 Մոդուլներ (C++20)

`#include` հրահանգի օգտագործումը շատ երկար պատմությունը ունի, հակված է սխալների և ծրագիրը տարբեր մասերից կառուցելու բավականին թանկ եղանակ է։ Եթե `header.h` ֆայլը `#include` հրամանով կցեք 101 թարգմանության միավորի, ապա կոմպիլյատորը 101 անգամ էլ կմշակի `header.h`-ի տեքստը։ Եթե `header1.h` ֆայլը կցեք `header2.h` ֆայլից առաջ, ապա `header1.h`-ի հայտարարություններն ու մակրոները կարող են ազդել `header2.h`-ի իմաստի վրա։ Եվ հակառակը՝ եթե `header2.h` ֆայլն եք կցել `header1.h` ֆայլից առաջ, ապա `header2.h`-ը կարող է ազդել `header1.h`-ի կոդի վրա։ Ակնհայտ է, որ սա կատարյալ եղանակ չէ, և փաստացի, 1972 թվականից սկսած, երբ այդ մեխանիզմը ներմուծվել է C լեզվում, այն եղել է թարգմանության արժեքի ու սխալների հիմնական աղբյուրը։

Մենք կարծես թե վերջապես մոտ ենք C++ լեզվում ֆիզիկական մոդուլների ներկայացման ավելի լավ եղանակ ունենալուն։ `module` կոչվող լեզվական հնարավորությունը դեռ ISO C++ ստանդարտի մաս չէ, բայց արդեն ներկայացված է որպես ISO տեխնիկական նկարագրություն (ISO Technical Specification) [ModulesTS]։ Արդեն որոշ իրականացումներ կիրառության մեջ են մտել, և այդ պատճառով էլ ես համարձակվում եմ `module`-ները ներկայացնել այստեղ՝ չնայած այն մեծ հավանականությանը, որ ինչ-որ ճշտումներ այնուամենայնիվ կարվեն, և միգուցե պետք կլինեն մի քանի տարիներ, մինչև մոդուլները կօգտագործվեն արտադրական կոդերում։ `#include` հրահանգներ օգտագործող հին կոդը կարող է դեռ շատ երկար «ապրել», քանի որ դրա արդիականացումը կարող է ժամանակատար ու ծախսատար լինել։

Դիտարկենք, թե ինչպես կարելի է `module`-ների օգտագործմամբ արտահայտել §3.2-ի `Vector`-ի և `sqrt_sum()`-ի օրինակը.

```C++
// Vector.cpp ֆայլը

module;  // այս թարգմանության միավորը սահմանում է մոդուլ

// ... այստեղ կգրենք այն ամենը, ինչ հարկավոր է Vector-ի իրականացման համար ...

export module Vector;  // "Vector" անունով մոդուլի սահմանումը

export class Vector {
public:
    Vector(int s);
    double& operator[](int i);
    int size();
private:
    double* elem;     // elem-ը ցուցիչ է sz հատ double-երի զանգվածին
    int sz;
};

Vector::Vector(int s)
    :elem{new double[s]}, sz{s}   // անդամների արժեքավորումը
{
}

double& Vector::operator[](int i)
{
    return elem[i];
}

int Vector::size()
{
    return sz;
}

export int size(const Vector& v) { return v.size(); }
```

Այստեղ սահմանված է `Vector` անունով `module`-ը, որը տրամադրում է (exports) `Vector` դասը, նրա բոլոր ֆունկցիա-անդամները, և `size()` ֆունկցիան։

Այս սահմանված մոդուլն օգտագործելու համար պետք է այն `import` հրահանգով ներմուծենք մեզ հարկավոր տեղում։ Օրինակ.

```C++
// user.cpp ֆայլը

import Vector;         // ներմուծել Vector-ի ինտերֆեյսը
#include <cmath>       // կցել ստանդարտ գրադարանի մաթեմատիկական ֆունկցիաների ինտերֆեյսը, ներառյալ sqrt()-ինը

double sqrt_sum(Vector& v)
{
    double sum = 0;
    for (int i=0; i!=v.size(); ++i)
        sum += std::sqrt(v[i]);   // քառակուսի արմատների գումարը

    return sum;
}
```

Ես կարող էի `import`-ի օգնությամբ ներմուծել ստանդարտ գրադարանի մաթեմատիկական ֆունկցիաների հայտարարությունները նույնպես, բայց օգտագործեցի հին ոճի `#include`-ը՝ պարզապես ցույց տալու համար որ հին ու նոր եղանակերը կարելի է համատեղել։ Այսպիսի համատեղումը կարևոր է հին `#include`-ներով կոդը նոր `import`-ներով կոդի աստիճանական ձևափոխման համար։

Վերնագրային ֆայլերի ու մոդուլների տարբերությունը միայն գրելաձևի մեջ չէ։

* Մոդուլը կոմպիլյացվում է միայն մեկ անգամ (այլ ոչ թե ամեն մի թարգմանության միավորի հետ, որտեղ այն օգտագործվում է)։
* Երկու մոդուլներ կարող են ներմուծվել կամայական հաջորդականությամբ՝ առանց իրենց իմաստը փոխելու։
* Եթե ինչ-որ բան ներմուծել եք մոդուլ, ապա այդ ներմուծածը մատչելի չէ ձեր մոդուլի օգտագործողներին. ներմուծումը տրանզիտիվ չէ։

Մոդուլների ազդեցությունը կոդի սպասարկելիության և կոմպիլյացիայի ժամանակի արտադրողականության վրա կարող է շատ տպավորիչ լինել։

## 3.4 Անունների տիրույթներ

Ի լրումն ֆունկցիաների (§1.3), դասերի (§2.3) և թվարկումների (§2.5), C++ լեզուն _անունների տիրույթներն_ (namespace) առաջարկում է որպես մի մեխանիզմ՝ նշելու համար, որ որոշ արտահայտություններ կապված են միմյանց և նրանց անունները չպետք է շփոթվեն այլ անունների հետ։ ?? Օրինակ, ես կարող եմ մի այդպիսի փորձ անել իմ սեփական կոմպլեքս թվերի տիպի հետ․ (§4.2.1, §14.4):

```C++
namespace My_code {
    class complex {
        // ...
    };

    complex sqrt(complex);
    // ...
    int main();
}

int My_code::main()
{
    complex z{1,2};
    auto z2 = sqrt(z);
    std::cout << '{' << z2.real() << ',' << z2.imag() << "}\n";
    // ...
}

int main()
{
    return My_code::main();
}
```

Իմ կոդը `My_code` առանձին անունների տիրույթի մեջ վերցնելով, ես վստահեցնում եմ, որ իմ անունները հակասության մեջ չեն մտնում ստանդարտ գրադարանի անունների հետ, որոնք գտնվում են `std` անունների տիրույթում (§3.4)։ Սա խելամիտ զգուշավորություն է, քանի որ ստանդարտ գրադարանը նույնպես պարունակում է `complex` թվերի թվաբանության հնարավորություններ (§4.2.1, §14.4)։

Որևէ անունների տիրույթում գտնվող անվանը դիմելու ամենապարզ եղանակը նրա անունը անունների տիրույթով լրացնելն է (օր․ `std::cout` և `My_code::main`)։ «Իրական `main()`-ը» սահմանված է անունների գլոբալ տիրույթում, այսինքն այն լոկալ չէ սահմանված անունների տիրույթում, դասում կամ ֆունկցիայում։ ???

Եթե անունների տիրույթների անունների բազմակի օգտագործումը ձանձրացնում է կամ ուշադրություն է շեղում, ապա այն ընթացիկ անունների տիրույթ կարելի է ներմուծել `using` հրահանգով․

```C++
void my_code(vector<int>& x, vector<int>& y)
{
    using std::swap;        // ստանդարտ գրադարանի swap-ը
    // ...
    swap(x,y);              // std::swap()
    other::swap(x,y);       // մի ուրիշ swap()
    // ...
}
```

`using`-հայտարարությունը ինչ-որ անունների տիրույթի անունը կիրառելի է դարձնում այնպես, կարծես թե այն հայտարարված է եղել իր օգտագործման անունների տիրույթում։ `using std::swap` արտահայտությունից հետո `swap`-ի կիրառության գրելաձևը ճիշտ այնպիսին է, ինչպես այն կլիներ `my_code()`-ում հայտարարելիս։

Ստանդարտ գրադարանի բոլոր անունները հասանելի դարձնելու համար կարող ենք օգտագործել հետևյալ `using` հրահանգը.

```C++
using namespace std;
```

`using` հրահանգը անվանված տիրույթի չդասակարգված (unqualified) անունները հասանելի է դարձնում այն տիրույթում, որում ինքն օգտագործված է։ Այսինքն, `std`-ի համար գրված `using` հրահանգից հետո, կարող ենք `std::cout`-ի փոխարեն պարզապես գրել `cout`։ `using` հրահանգի օգտագործմամբ կորցնում ենք տվյալ անունների տիրույթի անունների ընտրողաբար կիրառման հնարավորությունը, այդ պատճառով էլ այս միջոցը պետք է զգուշորեն օգտագործել, որպես կանոն միայն այնպիսի գրադարանի համար, որը լայնորեն օգտագործվում է ծրագրում (օր. `std`) կամ էլ այնպիսի ծրագրի ձևափոխության դեպքում, որը անունների տիրույթներ չի ունեցել։

Անունների տիրույթները առավելապես օգտագործվում են ծրագրային խոշոր բաղադրիչները կազմակերպելու համար, ինչպիսիք են գրադարանները։ Դրանք հեշտացնում են ծրագիրը առանձին մշակված մասերից կառուցելու գործը։


## 3.5  Սխալների մշակում

Սխալների մշակումը ծավալուն ու բարդ թեմա է՝ իր խնդիրներով ու ճյուղավորումներով, որոնք դուրս են գալիս  լեզվական միջոցների տիրույթից և տարածվում են ծրագրավորման տեխնիկայի և գործիքների տիրույթում։ C++-ը, այնուամենայնիվ, որոշ օգնական հնարավորություններ տրամադրում է։ Հիմնական գործիքը հենց ինքը տիպերի համակարգն է։ Ներդրված տիպերով (օրինակ, `char`, `int`, և `double`) և հրամաններով (օրինակ, `if`, `while` և `for`) մեր ծրագրերը մանրակրկիտ կառուցելու փոխարեն կառուցում ենք նոր տիպեր (օրինակ, `string`, `map` և `regex`) և ալգորիթմներ (օրինակ, `sort()`, `find_if()` և `draw_all()`), որոնք ավելի հարմար են մեր ծրագրին։ Այսպիսի բարձր մակարդակի կառուցվածքները պարզեցնում են ծրագրավորման ընթացքը, սահմանափակում են սխալների հնարավորությունները (օրինակ, դժվար թե դուք ծառով անցման ալգորիթմը կիրառեք երկխոսության պատուհանի նկատմամբ), և մեծացնում են կոմպիլյատորի՝ սխալներ հայտնաբերելու հնարավորությունները։ C++-ի լեզվական կառուցվածքների մեծամասնությունը նախատեսված է էլեգանտ և որակյալ աբստրակցիաներ նախագծելու և իրականացնելու համար (օրինակ, օգտագործողի սահմանած տիպերը և դրանց օգտագործող ալգորիթմները)։ Այսպիսի աբստրակցիայի արդյունքներից մեկն էլ այն է, որ կատարման ժամանկ հայտնաբերվող սխալի կետը կաելի է առանձնացնել այն կետից, որտեղ ախալը մշակվելու է։ Քանի որ ծրագրերը մեծանում են, և հատկապես երբ գրադարանները ավելի լայնորեն են կիրառվում, սխալների մշակման ստանդարտները դառնում են կարևոր։ Լավ գաղափար է ձևավորել և հստակեցնել սխալների մշակման ստրատեգիան ծրագրերի մշակման ավելի վաղ փուլերում։ ???

### 3.5.1 Բացառություններ

Նորից դիտարկենք `Vector` դասի օրինակը։ Ի՞նչ _պետք է_ անել այն դեպքում, երբ փորձում ենք դիմել §2.3֊ում սահմանված վեկտորի սահմաններից դուրս գտնվող տարրին։

* `Vector` դասը գրողը չգիտե, թե ինչպես կուզենա վարվել օգտագործողը նման իրավիճակում (սովորաբար `Vector`-ը գրողը նույնիսկ չգիտի էլ, թե որ ծրագրում է վեկտորն աշխատելու)։
* `Vector` դասի օգտագործողը չի կարող հետևողականորեն հայտնաբերել տվյալ խնդիրը (եթե օգտագործողը կարողանար, սահմանից դուրս դիմումը տեղի չէր ունենա ընդհանրապես)։

Ենթադրելով, որ սահմաններից դուրս դիմելն այն տիպի սխալներից է, որոնց դեպքում ուզում ենք վերականգնել ծրագրի աշխատանքը, լուծումը հետևյալն է. `Vector`֊ը իրականացնողը հայտնաբերում է զանգվածի սահմանից դուրս դիմելու փորձը և օգտագործողին տեղեկացնում է այդ մասին։ Այնուհետև օգտագործողը կարող է համապատասխան որոշում կայացնել։ Օրինակ, `Vector::operator[]()` ֆունկցիան կարող է հայտնաբերել սահմաններից դուրս դիմման փորձն ու դուրս նետել (throw) `out_of_range` բացառությունը․

```C++
double& Vector::operator[] (int i)
{
    if (i < 0 || size() <= i)
        throw out_of_range{"Vector::operator[]"};
    return elem[i];
}
```

To do that, the implementation will unwind the function call stack as needed to get back to the context of that caller. That is, the exception handling mechanism will exit scopes and functions as needed to get back to a caller that has expressed interest in handling that kind of exception, invoking destructors (§4.2.2) along the way as needed. For example:

`throw`-ն ղեկավարումը փոխանցում է մի որևէ ֆունկցիայում `out_of_rang` տիպի բացառությունների մշակիչին (exception handler), որից ուղղակիորեն կամ անուղղակիորեն կանչվել է `Vector::operator[]()`-ը։ ?? Դա անելու համար իրականացումն, ըստ անհրաժեշտության, _ետ է քանդում_ (unwind) ֆունկցիաների կանչի ստեկը, որպեսզի հասնի այդ կանչող ֆունկցիայի կոնտեքստին։ Այսինքն, պետք եղած դեպքում, բացառությունների մշակման մեխանիզմը դուրս կգա տեսանելիության տիրույթներից ու ֆունկցիաներից, որպեսզի հետ գնա այն կանչող ֆունկցիային, որը հետաքրքրություն է ցուցաբերել տվյալ տիպի բացառությունների մշակումով, հընթացս կանչելով անհրաժեշտ դեստրուկտորները (§4.2.2): Օրինակ.

```C++
void f(Vector& v)
{
    // ...
    try { // այստեղից դուրս նետված բացառությունները մշակվում 
          // են ստորև սահմանված catch բլոկում

        v[v.size()] = 7;   // փորձել դիմել v֊ի սահմաններից դուրս
    }
    catch (out_of_range& err) { // հոպլյա՜. out_of_range սխալ
        // ... մշակել միջակայքից դուրս գալու սխալը ...
        cerr << err.what() << '\n';
    }
    // ...
}
```

Այն կոդը, որում առաջացած բացառությունների մշակմամբ հետաքրքրված ենք, վերցնում ենք `try` բլոկի մեջ։ `v[v.size()]`-ին վերագրման փորձը կձախողվի։ Հետևաբար, կմտնենք այն `catch` բլոկի մեջ, որը նախատեսված է `out_of_range` տիպի բացառությունների մշակման համար։ `out_of_range` տիպը սահմանված է ստանդարտ գրադարանում (`<stdexcept>` ֆայլում) և փաստացի օգտագործվում է ստանդարտ գրադարանի` կոնտեյներների տարրերին դիմող ֆունկցիաներում։

Պատճենումից խուսափելու համար ես բացառությունը որսացել (catch) եմ հղումով, իսկ `what()` ֆունկցիան օգտագործել եմ `throw`-ի կետում բացառությանը փոխանցված սխալի հաղորդագրությունը արտածելու համար։

Բացառությունների մշակման մեխանիզմների օգտագործումը կարող է սխալների մշակումը դարձնել ավելի պարզ, ավելի համակարգված և ավելի ընթեռնելի։ Այդ նպատակին հասնելու համար պետք չէ չարաշահել `try`-հրամանները։ Սխալների մշակումը պարզ և համակարգված դարձնելու գլխավոր տեխնիկան (որը կոչվում է Resource Aquisition Is Initialization; RAII) բացատրված է §4.2.2.-ում։ RAII-ի հիմնական գաղափարն այն է, որ կոնստրուկտորը զբաղեցնում է դասի գործունեության համար անհրաժեշտները ռեսուրսները, իսկ դեստրուկտորը ազատում է դրանք բոլորը, այդպիսով ռեսուրսների ազատում դարձնելով երաշխավորված և անբացահայտ։ ??

Այն ֆունկցիան, որը երբեք չպետք է բացառություն դուրս նետի, կարող է հայտարավել `noexcept`։ Օրինակ․

```C++
void user(int sz) noexcept
{
    Vector v(sz);
    iota(&v[0], &v[sz], 1); // v֊ն լրացնել 1,2,3,4...-ով
}
```

If all good intent and planning fails, so that user() still throws, std::terminate() is called to immediately terminate the program.

Եթե բոլոր բարի նպատակներն ու ծրագրերը ձախողվում են, իսկ `user()` ֆունկցիան շարունակում բացառություն դուրս նետել, ապա կանչվում է ստանդարտ գրադարանի `terminate()` ֆունկցիան, որն էլ անհապաղ ավարտում է ծրագրի կատարումը։


### 3.5.2 Ինվարիանտներ

Կոնտեյների սահմաններից դուրս դիմման մասին ազդարարելու համար բացառությունների օգտագործումը ֆունկցիայի օրինակ է, որը ստուգում է իր արգումենտները և մերժում հետագա կատարումը, քանի որ հիմնական ենթադրությունը, _նախապայմանը_, տեղի չի ունենում։ ?? Եթե ճշգրտորեն սահմանեինք `Vector`-ի ինդեքսավորման գործողությունը, ապա կասեինք ասյպիսի մի բան. «ինդեքսը պետք է լինի `[0:size())` միջակայքում», և դա փաստորեն հենց այն է, ինչ ստուգում ենք մեր `operator[]()` ֆունկցիայում։ `[a:b)` գրառումով որոշվում է կիսաբաց միջակայք, որում `a`֊ն այդ միջակայքից է, իսկ `b`֊ն՝ ոչ։ Ամեն անգամ, երբ սահմանում ենք ֆունկցիա, պետք է լավ մտածենք, որոնք են նրա նախապայմանները և հնարավոր է արդյոք դրանք ստուգել։ Ամեն մի ֆունկցիա սահմանելիս պետք է որոշենք, թե որոնք են դրա նախապայմանները, և որոշենք, թե ինչպես ենք դրանք ստուգելու (§3.5.3)։ Շատ դեպքերում լավ գաղափար է ստուգել պարզ ինվարիանտները (տես նաև §3.5.4):

Սակայն `operator[]()` ֆունկցիան աշխատում է `Vector` տիպի օբյեկտների հետ, և նրա գործողություններից ոչ մեկն իմաստ չի ունենա, քանի դեռ `Vector`-ի անդամները «խելամիտ» արժեքներ չունեն։ Մասնավորապես ասացինք, որ `elem`-ը ցույց է տալիս `sz`հատ `double`-ների զանգվածի», բայց դա ասել ենք միայն մեկնաբանության մեջ։ Այսպիսի պնդումը, թե տվյալ դասի համար ի՛նչը պետք է ճշմարիտ լինի, կոչվում է _դասի ինվարիանտ_, կամ պարզապես _ինվարիանտ_։ Կոնստրուկտորի գործն է՝ իր դասի համար սահմանել ինվարիանտը (այնպես, որ անդամ ֆունկցիաները կարողանան հաշվի առնել այն), իսկ անդամ ֆունկցիաների գործն է իրենց ավարտից հետո երաշխավորել ինվարիանտի տեղի ունենալը։ ?? Դժբախտաբար մեր `Vector`-ի կոնստրուկտորը դա միայն մասնակիորեն է անում։ Այն պատշաճ ձևով արժեքավորել է `Vector`-ի անդամները, բայց չի ստուգում, թե արդյոք իրեն փոխանցված արգումենտներն իմաստ ունեն։ Դիտարկենք հետևյալը.

```C++
Vector v(-27);
```

Սա հավանաբար քաոս է առաջացնելու։ ??

Ահա ավելի ճիշտ սահմանումը․

```C++
Vector::Vector(int s)
{
    if (s < 0)
        throw length_error{"Vector-ի կոնստրուկտորը: չափը բացասական է"};
    elem = new double[s];
    sz = s;
}
```

Տարրերի ոչ դրական չափի մասին ազդարարելու համար ես օգտագործում եմ ստանդարտ գրադարանի `length_error` բացառությունը, որովհետև նմանատիպ խնդիրների համար ստանդարտ գրադարան որոշ գործողություններ նույնպես այդ բացառությունն են օգտագործում։ ?? Եթե `new` գործողությունը չի կարողանում հիշողություն առանձնացնել, ապա այն դուրս է նետում `std::bad_alloc` բացառությունը։ Հիմա կարող ենք գրել.

```C++
void test()
{
    try {
        Vector v(-27);
    }
    catch (std::length_error& err) {
        // մշակել բացասական չափի դեպքը
    }
    catch (std::bad_alloc& err) {
        // մշակել հիշողության պակասի դեպքը
    }
}
```

Դուք կարող եք սահմանել բացառությունների ձեր սեփական դասերը, և պահանջել, որ նրանք սխալի հայտնաբերման կետից կամայական տեղեկություն փոխանցեն այն կետին, որտեղ դրան կարող են մշակվել (§3.5.1)։ ??

Հաճախ, բացառության տեղի ունենալու դեպքում, ֆունկցիան հնարավորություն չի ունենում ավարտել իրեն վերագրված առաջադրանքը։ Այդ դեպքում բացառություն «մշակելը» հանգում է ռեսուրսների ազատման ինչ-որ մինիմալ գործողությունների, որից հետո նորից բացառություն է գեներացվում։ ?? Օրինակ.

```C++
void test()
{
    try {
        Vector v(-27);
    }
    catch (std::length_error&) {
        cout << "ստուգումը ձախողվել է. սխալ չափ\n";
        throw;   // նորից գեներացնել
    }
    catch (std::bad_alloc&) {
        // Հոպլյա՜։ Այս ֆունկցիայում հիշողության պակասի
        // դեպքը մշակելը նախատեսված չէ
        std::terminate();   // ավարտել ծրագիրը
    }
}
```

Խնամքով նախագծված կոդում `try` բլոկները հազվադեպ են հանդիպում։ Խուսափեք դրանց չափից շատ օգտագործումից՝ հետևողականորեն օգտագործելով RAII տեխնիկան(§4.2.2, §5.3)։

Դասերի նախագծման համար ինվարիանտի գաղափարը շատ կարևոր է, իսկ նախապայմանները նույն դերն են կատարում ֆունկցիաների նախագծման ժամանակ։ ?? Ինվարիանտներն.

* օգնում են ճշգրիտ հասկանալ, թե ինչ ենք ուզում,
* ստիպում են մեզ ավելի ճշգրիտ լինել. դա ավելի լավ հնարավորություն է տալիս (շտկումից ու թեսթավորումից հետո) ստանալ աշխատող կոդ։

C++ լեզվի ռեսուրսների կառավարման գաղափարը, կոնստրուկտորներով (Գլուխ 4]) ու դեստրուկտորներով ապահովվող (§4.2.2, §13.2), հիմնված է ինվարիանտների գաղափարի վրա։ ??


### 3.5.3 Սխալների մշակման այլընտրանքային եղանակներ

Իրական կյանքում հանդիպող բոլոր ծրագրերում սխալների մշակումը գլխավոր խնդիրն է, և այդ պատճառով էլ գոյություն ունեն տարբեր մոտեցումներ։ Եթե սխալ է հայտնաբերվել և այն չի կարող մշակվել ֆունկցիայի մեջ, ապա ֆունկցիան պետք է մի որևէ եղանակով այդ խնդրի մասին հաղորդի ինչ-որ կանչող ֆունկցիայի։ ?? C++ լեզվում դա անելու ամենաընդհանուր մեխանիզմը բացառություն դուրս նետելն է։ ??

Կան լեզուներ, որոնցում բացառությունները նախատեսված են պարզապես որպես արժեք վերադարձնելու այլընտրանքային եղանակ։ C++-ն այդպիսի լեզուներից չէ. բացառությունները նախատեսված են տրված խնդրի ավարտը ձախողվելի մասին ազդարարելու համար։ ?? Բացառություններն ինտեգրված են կոնստրուկտորներում ու դեստրուկտորներում, որպեսզի տրամադրեն սխալների մշակման և ռեսուրսների կառավարման կոհերենտ ? միջոց (§4.2.2, §5.3)։ ?? Կոմպիլյատորներն օպտիմիզացված են այնպես, որ արժեք վերադարձնելն ավել ծախսատար չլինի, քան նույն այդ արժեքի դուրս նետելը բացառության օգնությամբ։ ??

Բացառություն գեներացնելը լոկալ եղանակով սխալի մշակման անհնարինության մասին ազդարարելու միակ եղանակը չէ։ Ֆուկցիան կարող է ցույց տալ, որ ինքը չի կարողանում կատարել նախանշված խնդիրը, հետևյալ եղանակներով.

* բացառություն գեներացնելով,
* վերադարձնել սխալի մասին ցույց տվող ինչ-որ արժեք,
* ծրագիրն ավարտելով (`terminate()`, `exit()`, `abort()` կամ նմանատիպ մի ֆունկցիա կանչելով)։

Վերադարձնում ենք սխալի որևէ հայտանիշ («սխալի կոդ») երբ.

* Սխալը նորմալ է և սպասելի։ Օրինակ, բոլորովին նորմալ է երբ ֆայլը բացելը ձախողվում է (դուցե այդ անունով ֆայլ չկա, կամ էլ ֆայլը չի կարող բացվել առկա իրավունքներով)։ ?? 
* Անմիջական կանչող ֆունկցիան կարող է պատշաճ եղանակով մշակել սխալը։

Բացառություն ենք գեներացնում երբ.

* Սխալն այնքան հազվադեպ է հանդիպում, որ շատ հնարավոր է, որ ծրագրավորողը մոռանա ստուգել այն։ Օրինակ, ե՞րբ եք վերջին անգամ ստուգել `printf()`-ի վերադարձրած արժեքը։
* Սխալը չի կարող մշակվել անմիջական կոնչող ֆունկցիայի կողմից։ Դրա փոխարեն այն պետք է փոխանցվի մի ուրիշ, ավելի վերև մակարդակի կանչողի։ Օրինակ, հնարավոր չէ, որ ծրագիր յուրաքանչյուր ֆունկցիան հուսալիորեն մշակի հիշողության առանձնացման կամ ցանցի խափանման սխալները։
* Սխալների նոր տեսակներ կարող են ավելացվել ծրագրի ցածր մակարդակի մոդուլներում այնպես, որ բարրձր մակարդակի մոդուլները գրված չեն եղել բայդպիսի սխալները մշակելու համար։ ?? Օրինակ, երբ ի սկզբանե մեկ հոսքով աշխատող ծրագրը ձևափոխվում է մի քանի հոսքեր օգտագործելու կամ ցանցի միջոցով ռեսուրսներին հասանելիություն ունենալու համար։ ??
* Սխալի կոդը վերադարձնելու հարմար հարմար ճանապարհ չկա։ Օրինակ, կոնստրուկտորը վերադարձվող արժեք չունի, որպեսզի կանչող ֆունկսիան այն ստուգի։ Բացի այդ, կոնստրուկտոր կարող է կանչվել մի քանի լոկալ օբյեկտների համար, կամ դեռ ամբողջովին չկառուցված բարդ օբյեկտի համար այնպես, որ սխալների կոդերի օգտագործմամբ մաքրումը կարող է բավականին բարդ լինել։ ??
* Պարզվում է, որ արժեքները և սխալի հայտանիշը (օրինակ, `pair`-ով, §13.4.3) ֆունկցիայից վերադարձնելը շատ ավելի բարդ կամ ծախսատար է, որը կարող է բերել ավելորդ պարամետրերի օգտագործման, ոչ լոկալ սխալի ստատուսներ պահելու կամ այլ շրջանցող եղանակների (workarounds)։ ?? 
* Սխալը պետք է կոնչի շղթայով փոխանցվի «ամենավերևի կանչող ֆունկցիային»։ Այդ ճանապարհին սխալի ստատուսը պարբերաբար ստուգելը կարող է լինել ձանձրալի, ծախսատար ու հանգեցնել այլ սխալների։
* Սխալի հայտնաբերումից հետո ծրագրի աշխատանքի վերականգնումը կախված է մի քանի ֆունկցիաների կանչից, որի հետևանքով կարիք է առաջանում կանչերի միջև լոկալ վիճակներ պահելու և բարդ ղեկավարող կառուցվածքների։ ???
* Սխալը հայտնաբերած ֆունկցիան հետադարձ կանչի ֆունկցիա (callback) է եղել, և անմիջական կանչող ֆունկցիան չգիտի էլ, թե ինչ ֆունկցիա է կանչվել։ ??
* Սխալը պանանջում է որոշակի «գործողությունների չեղարկում»։ ??

Ավարտում ենք ծրագրի աշխատանքը երբ.

* Այնպիսի սխալ է, որից հետո չենք կարող վերականգնել ծրագրի աշխատանքը։ Օրինակ, շատ համակարգերում (բայց ոչ բոլորում) ոչ մի խելամիտ եղանակ չկա աշխատանքը շարունակել հիշողությունը սպառվելուց հետո։
* Այնպիսի համակարգ է, որտեղ ոչ տրիվիալ սխալների մշակումը հնարավոր չէ առանց հոսքի, պրոցեսի կամ մեքենայի վերագործարկման։ ??

Ծրագրի ավարտը երաշխավորելու եղանակներից մեկը ֆունկցիային `noexcept` ավելացնելն է, որի արդյունքում ֆունկցիայի իրականացման կամայական տեղում `throw`-ի կիրառումը կբերի `terminate()`-ի կանչի։ Նկատենք, որ կան այնպիսի գրագրեր, որոնցում ընդունելի չէ ծրագրի անպայման ավարտը, հետևաբար պետք է փնտրել այլընտրանքներ։ ??

Դժբախտաբար միշտ չէ, որ վերը թվարկված պայմաննները տրամաբանորեն տարանջատված են և դրանց կիրառումը հեշտ է։ ?? Նշանակություն ունի նաև ծրագրի չափն ու բարդությունը։ Երբեմն ծրագրի զարգացման հետ փոփոխվում են նաև դրա վրա դրված պահանջները։ Անհրաժեշտ է փորձ։ Եթե երկմտանքի մեջ եք, նախապատվությունը տվեք բացառություններին, որովհետև դրանց դրանց օգտագործումը հեշտ ընդլայնելի է և այլ արտաքին գործիքների չի պահանջում, համոզվելու համար որ բոլոր սխալները մշակված են։ ??

Մի՛ կարծեք, թե բոլոր սխալների կոդերը կամ բոլոր բացառությունները վատն են. երկուսի համար էլ կան հստակ որոշված կիրառություններ։ Այնուհետև, մի հավատացեք այն առասպելին, թե բացառությունների մշակումը դանդաղ է. այն հաճախ ավելի արագ է, քան բարդ կամ հազվադեպ հանդիպող սխալների ճիշտ մշակումը, և սկալների կոդերի կրկնվող ստուգումները։

RAII-ն (§4.2.2, §5.3) բացառությունների օգտագործմամբ սխալների մշակման պարզ ու արդյունավետ եղանակի հիմնարար սկզբունքն է։ `try` բլոկներով խճողված կոդը հաճախ պարզապես արտացոլում է սխալների կոդերի վրա հիմնված սխալների մշակման վատագույն ավանդույթները։ ??

### 3.5.4 Կոնտրակտներ

Ինվարիանտների, նախապայմանների և այլնի համար լրացուցիչ կատարման ժամանակի ստուգումներ գրելու ընդհանուր եղանակ առայժմ չկա։ Կոնտրակտների մեխանիզմը առաջարկվել է C++20-ում [Garcia,2016] [Garcia,2018]։ Նպատակն է աջակցել այն օգտագործողներին, ովքեր ուզում են ծրագրի ճիշտ աշխատանքի համար հենվել թեսթավորման վրա, գործարկելով էքստենսիվ կատարման ժամանակի ստուգումներ, իսկ հետո թողարկել նվազագույն ստուգումներով կոդ։ ?? Այսպիսի մոտեցումն ընդունված է բարձր արտադրողականության կիրառությունների համար այնպիսի կազմակերպություններում, որոնք հենվում են համակարգված և էքստենսիվ ստուգումների վրա։ ??

Մինչ այժմ ստիպված էինք օգտագործել տվյալ պահին ու տեղին հարմար միջոցներ։ Օրինակ, կատարման ժամանակի ստուգումները կարգավորելու համար կարող ենք օգտագործել հրամանային տողի ինչ-որ մակրո։ ??

```C++
double& Vector::operator[](int i)
{
    if (RANGE_CHECK && (i<0 || size()<=i))
        throw out_of_range{"Vector::operator[]"};
    return elem[i];
}
```

Համոզվելու համար, որ ստուգումը կատարվում է կատարման ժամանակ, ստանդարտ գրադարանն առաջարկում է `assert()` շտկման մակրոն։ ??

```C++
void f(const char* p)
{
    assert(p!=nullptr);  // p-ն չպետք է nullptr լինի
    // ...
}
```

Եթե `assert()`-ի պայմանի ստուգումը ձախողվում է «շտկման ռեժիմում» (debug mode), ապա ծրագրի աշխատանքն ավարտվում է։ Եթե շտկման ռեժիմում չենք, ապա `assert()`-ը չի ստուգվում։ Բավականին կոպիտ և ոչ ճկուն է, բայց սա հաճախ բավական է լինում։

### 3.5.5 Ստատիկ պնդումներ

Բացառությունները հայտնում են ծրագրի կատարման ժամանակի սխալների մասին։ Եթե հնարավոր է սխալը հայտնաբերել կոմպիլյացիայի ժամանակ, ապա սովորաբար նախընտրելի է անել հենց այդպես։ Ահա թե ինչի համար են կարևոր տիպերի համակարգը և օգտագործողի սահմանած տիպերի ինտերֆեյսների սահմանման միջոցները։ Այնուամենայնիվ, կարող ենք կոմպիլյացիայի ժամանակ հայտնի շատ հատկությունների վրա պարզ ստուգումներ անել, և կոմպիլյացիայի սխալի հաղորդագրությունների տեսքով ազդարարել մեր ակնկալիքների չհամընկնելու մասին։ ?? Օրինակ․

```C++
static_assert(4 <= sizeof(int)), "integers are too small"); // ստուգեք ամբողջ տիպի չափը
```

Եթե `4 <= sizeof(int)` տեղի չունի, այսինքն՝ եթե տվյալ համակարգում `int`-ի չափը առնվազն 4 բայթ չէ, ապա այս կոդը կարտածի `integers are too small`: Ակնկալիքների այսպիսի հրամաններն անվանում ենք _ստատիկ պնդումներ_։

`static_assert`-ի մեխանիզմը կարելի է կիրառել ամեն ինի նկատմամբ, որ հնարավոեր է ներկայացնել հաստատուն արտահայտություններով (§1.6)։ ?? Օրինակ.

```C++
constexpr double C = 299792.458; // կմ/վ

void f(double speed)
{
    const double local_max = 160.0 / (60*60);   // 160 կմ/վ = 160.0 / (60*60) կմ/վ
    static_assert(speed < C, "can't go that fast");   // սխալ․ արագությունը պետք է հաստատուն լինի
    static_assert(local_max < C, "can't go that fast");   // ճիշտ է
    //...
}
```

Ընդհանուր դեպքում, եթե `A`-ն `true` չէ, ապա `static_assert(A,S)` հրամանը `S`-ը տպում է որպես կոմպիլյատորի սխալի հաղորդագրություն։ Եթե չեք ուզում ինչ-որ հատուկ հաղորդագրություն, ապա բաց թողեք `S`-ը և կոմպիլյատորը կարդածի լռելությամբ նախատեսված հաղորդագրությունը։

```C++
static_assert(4<=sizeof(int));  // օգտագործել լռելության հաղորդագրությունը
```

Լռելության հաղորդագրությունը սովորաբար ծրագրի տեքստում `static_assert`-ի տեղն է և նրա արգումենտի արտահայտության նիշային ներկայացումը։

`static_assert`-ի ամենակարևոր կիրառությունը հանդիպում է այն ժամանակ, երբ ուզում ենք պնդումներ անել ընդհանրացված ծրագրավորման (§7.2, §13.9) պարամետրիզացված տիպերի մասին։


## 3.6 Ֆունկցիայի արգումենտներն ու վերադարձվող արժեքը

### 3.6.1 Արգումենտի փոխանցումը

### 3.6.2 Արժեք վերադարձնելը

### 3.6.3 Կապակցում ըստ կառուցվածքի


## 3.7 Խորհուրդներ

* Այս գլխի նյութը մոտավորապես համապատասխանում է նրան, ինչ որ ավելի մանրամասնորեն նկարագրված է [Stroustrup,2013] գրքի 13-15 գլուխներում։
* Դրե՛ք տարբերություն հայտարարությունների (որոնք օգտագործվել են որպես ինտերֆեյս) և սահմանումների (որոնք օգտագործվել են որպես իրականացում) միջև; §3.1։
* Օգտագործե՛ք վերնագրային ֆայլեր՝ ինտերֆեյսերը ներկայացնելու և ծրագրի տրամաբանական կառուցվածքն ընդգծելու համար; §3.2։
* `#include` (ներառե՛ք) վերնագրային ֆայլը այն սկզբնական ֆայլի մեջ, որն իրականացնում է իր ֆունկցիաները; §3.2։
* Խուսափե՛ք վերնագրային ֆայլերում ոչ֊ներդրված ֆունկցիաների սահմանումներից; §3.2։
* Օգտագործե՛ք անունների տիրույթներ՝ արտահայտելու համար ծրագրի տրամաբանական կառուցվածքը; §3.3։
* Օգտագործե՛ք `using`֊դիրեկտիվները փոխանցման համար, հիմքային գրադարանների (ինչպիսին է օրինակ `std`֊ն) համար կամ լոկալ տիրույթի ներսում; §3.3։
* Մի՛ տեղադրեք `using`-դիրեկտիվը վերնագրային ֆայլի մեջ; §3.3։
* Դուրս գրե՛ք բացառություններ՝ ցույց տալու համար, որ չեք կարողանում լուծել ձեզ տրամադրված խնդիրը; §3.4։
* Օգտագործե՛ք բացառությունները սխալների մշակման համար; §3.4։
* Զարգացրե՛ք սխալների մշակման ռազմավարությունը ավելի շուտ՝ ծրագրի ձևավորման մեջ; §3.4։
* Օգտագործե՛ք որպես բացառություններ նպատակային ձևավորված օգտագործողի կողմից սահմանված տիպերը (այլ ոչ թե ներդրված տիպերը); §3.4.1։
* Մի՛ փորձեք գտնել բոլոր բացառությունները յուրաքանչյուր ֆունկցիայի մեջ; §3.4։
* Եթե Ձեր ֆունկցիան չի կարող դուրս բերել բացառություններ, հայտարարե՛ք այն որպես `noexcept`; §3.4:
* Թույլ տվեք, որ կոնստրուկտորը հաստատի ինվարիանտը և դուրս բերի այն, եթե չի կարողանում; §3.4.2։
* Ձևավորե՛ք Ձեր սխալների մշակման ռազմավարությունը ինվարիանտների շուրջ; §3.4.2։
* Այն ինչ հնարավոր է ստուգել կոմպիլյացիայի ժամանակ, սովորաբար ամենալավ ձևով ստուգվում է հենց կոմպիլյացիայի ժամանակ (օգտագործելով `static_assert`֊ը); §3.4.3։