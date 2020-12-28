
# Կոնցեպտներ և ընդհանրացված ծրագրավորում

## Ներածություն

Ինչի՞ համար են կաղապարները (template)։ Այլ կերպ ասած՝ ծրագրավորման ո՞ր տեխնիկաներն են ավելի արդյունավետ, երբ օգտագործում եք կաղապարները։ Կաղապարներն առաջարկում են.

* Տիպերը (ինչպես նաև արժեքներն ու կաղապարները) որպես արգումենտ փոխանցելու հնարավորություն՝ առանց ինֆորմացիայի կորստի։ Սա ենթադրում է կոդի ներդրման (inlining) գերազանց հնարավորություններ, որն էլ կոմպիլյատորների ժամանակակից իրականացումներում մեծ առավելություն է տալիս։
* Օբյեկտի ստեղծման ժամանակ տարբեր ենթատեքստերից ստացված ինֆորմացիայի միահյուսման հնարավորություն։ Սա ենթադրում է նաև օպտիմիզացիայի հնարավորություն։
* Հաստատուն արժեքները որպես արգումենտներ փոխանցելու հնարավորություն։ Սա ենթադրում է կոմպիլյացիայի ժամանակի հաշվարկների հնարավորություն։

Այլ կերպ ասած՝ կաղապարները տրամադրում են կոմպիլյացիայի ժամանակի հաշվարկների և տիպերի մանիպույացիայի հզոր միջոց, որի արդյունքում կարող է ստացվել շատ կոմպակտ և արդյունավետ ծրագրային կոդ։ Հիշեք, որ տիպերը (դասերը) կարող են պարունակել և՛ կոդ (§6.3.2), և՛ տվյալներ (§6.2.2)։

Կաղապարների առաջին և ամենահաճախ օգտագործումը _ընդհանրացված ծրագրավորման_, այսինքն՝ ընդհանրացված ալգորիթմների նախագծմանը, իրականացմանը և օգտագործմանը միտված ծրագրավորման (generic programming) ապահովումն է։ Այստեղ «ընդհանրացված» բառը ցույց է տալիս ալգորիթմը կարող է նախագծվել տիպերի մեծ բազմության հետ օգտագործման համար՝ միայն թե այդ տիպերը բավարարեն ալգորիթմների արգումենտների վրա գրված պահանջները։ Կոնցեպտների հետ միասին կաղապարները C++ լեզվում ընդհանրացված ծրագրավորումն ապահովող հիմնական միջոցներն են։ Կաղապարները ապահովում են (կոմպիլյացաիյի ժամանակի) պարամետրիկ պոլիմորֆիզմ (բազմաձևություն)։


## Կոնցեպտներ (C++20)

Դիտարկենք §6.3.1-ի `sum()` ֆունկցիան.

````C++
template<typename Seq, typename Num>
Num sum(Seq s, Num v)
{
    for (const auto& x : s)
        v += x;
    return v;
}
````

Այն կարելի է կիրառել ցանկացած տվյալների կառուցվածքի հետ, որի `begin()`-ը և `end()`-ը  ապահովում են range-for-ի աշխատանքը։ Այդպիսի կառուցվածքներից են ստանդարտ գրադարանի `vector`-ը, `list`-ը և `map`-ը։ Բացի այդ, տվյալների կառուցվածքի տարրի տիպը սահմանափակված է միայն իր օգտագործմամբ. այն պիտի լինի այնպիսի տիպի, որը կարողանանք գումարել `v` արգումենտին։ Այդպիսիք են, օրինակ, `int`, `double` և `Matrix` տիպերը (`Matrix` մի որևէ ընդունելի իրականացման պայմանով)։ Կարող ենք ասել, որ `sum()` ալգորիթմը ընդհանրացված է (is generic) երկու չափողականություններով՝ տարրերը պարունակող տվյալների կառուցվածքի («հաջորդականության») տիպով և տարրերի տիպով։

Այսպիսով, `sum()`-ը պահանջում է, որ իր կաղապարի առաջին արգումենտը լինի ինչ-որ հաջորդականություն, իսկ կաղապարի երկրորդ արգումենտը՝ ինչ-որ թիվ։ Այսպիսի պահանջներն անվանում ենք _կոնցեպտներ_ (concepts)։

Կոնցեպտները դեռ լեզվի ISO C++ ստանդարտի մաս չեն, բայց ներկայացված են որպես ISO Technical Specification [ConceptsTS]։ Իրականացումներ արդեն կան, և ես հանձն եմ առնում այս գրքում ներկայացնել կոնցեպտները, չնայած որ ինչ-որ հատկություններ դեռ կփոփոխվեն, և հնարավոր է շատ տարիներ պահանջվեն, մինչև ամեն ոք կարող է դրանք օգտագործել արտադրական ծրագրերում։

### Կոնցեպտների օգտագործումը

Կաղապարների ճիշտ կոմպիլյացաիայի և գեներացված կոդի ճիշտ կատարման համար կաղապարների արգումենտներից շատերը պետք է բավարարեն տվյալ կաղապարին հատուկ ինչ-որ պայմանների։ Այսինքն՝ կաղապարներից շատերը պետք է լինեն սահմանափակումներով (constraints) (§6.2.1)։ Տիպի անունը բնորոշող `typename` բառը այն նվազագույն սահմանափակումն է, որը միայն պահանջում է արգումենտի տիպ լինելը։ Սովարաբար կարող ենք դրանից ավելի անել։ Նորից դիտարկենք այն `sum()` ֆունկցիան.

````C++
template<Sequence Seq, Number Num>
Num sum(Seq s, Num v)
{
    for (const auto& x : s)
        v += x;
    return v;
}
````

Ահա արդեն ավելի հստակ է։ Հենց որ սահմանենք, թե ինչ են նշանակում `Sequence` և `Number` կոնցեպտները, կոմպիլյատորը կարող է մերժել սխալ կանչերը՝ միայն `sum()`-ի ինտերֆեյսին, այլ ոչ թե նրա իրականացմանը նայելով։ Սա լավացնում է սխալների մասին ազդարարումը։

Սակայն `sum()`-ի ինտերֆեյսը դեռ լրիվ չէ. ես «մոռացա» ասել, որ պետք է կարողանանք `Sequence`-ի տարրերը գումարել `Number`-ին։ Դա էլ կարող ենք անել.

````C++
template<Sequence Seq, Number Num>
    requires Arithmetic<Value_type<Seq>,Num>
Num sum(Seq s, Num n);
````

Հաջորդականության համար `Value_type`-ը նրա տարրերի տիպն է։ `Arithmetic<X,Y>`-ը կոնցեպտ է, որով որոշվում է, որ `X` և `Y` տիպերի թվերի հետ կարող ենք թվաբանական գործողություններ կատարել։ Սա մեզ ապահովում է `vector<string>`-ի կամ `vector<int*>`-ի գումարը պատահաբար հաշվելու փորձերից, միևնույն ժամանակ թույլատրելով `vector<int>`-ը և `vector<complex<double>>`-ը։

Այս օրինակում մեզ անհրաժեշտ է միայն `+=`-ը, բայց, պարզության ու ճկունության համար, պետք չէ կաղապարի արգումենտն այդքան խիստ սահմանափակենլ։ Մասնավորապես, ինչ-որ մի օր կարող է ուզենանք `sum()`-ն արտահայտել `+` և `=` գործողությունների, այլ ոչ թե `+=`-ի միջոցով, և այդ ժամանակ հաճելի կլինի տեսնել, որ ընտրել ենք ավելի ընդհանուր կոնցեպտ (այս դեպքում, `Arithmetic`), քան պարզապես «ունենալ `+=`-ը» նեղ պահանջը։

Մասնակի սահմանումները, ինչպես կոնցեպտներնի օգտագործմամբ սահմանված առաջին `sum()`-ը, կարող են շատ օգտակար լինել։ Եթե սահմանումը լրիվ չէ, ապա որոշ սխալներ չեն կարող հայտնաբերվել մինչև նմուշի ստեղծումը։ (?)  However, partial specifications can help a lot, express intent, and are essential for smooth incremental development where we don’t initially recognize all the requirements we need. Կոնցեպտների հասուն գրադարանի առկայության դեպքում սկզբնական գրառումները շատ մոտ կլինեն կատարյալին։

Զարմանալի չէ, որ `requires Arithmetic<Value_type<Seq>,Num>` արտահայտությունը կոչվում է `requirements`-պայման։ `template<Sequence Seq>` գրառումը պարզապես `requires Sequence<Seq>` բացահայտ գրառման համառոտ տեսքն է։ Ավելի խոսուն լինելու համար կարող էի գրել հետևյալ համարժեքը.

````C++
template<typename Seq, typename Num>
    requires Sequence<Seq> && Number<Num> && Arithmetic<Value_type<Seq>,Num>
Num sum(Seq s, Num n);
````

Մյուս կողմից էլ, երկու գրառումների համարժեքությունն օգտագործելով, կարող ենք գրել.

````C++
template<Sequence Seq, Arithmetic<Value_type<Seq>> Num>
Num sum(Seq s, Num n);
````

Եթե ինչ-որ տեղ չենք կարող օգտագործել կոնցեպտները, ապա ստիպված ենք բավարարվել անուններ տալու պայմանավորվածություններով և նեկնաբանություններով։ Օրինակ, այսպես.

````C++
template<typename Sequence, typename Number>
    // requires Arithmetic<Value_type<Sequence>,Number>
Numer sum(Sequence s, Number n);
````

Ինչպիսի գրառում էլ որ ընտրելու լինենք, կարևոր է, որ կաղապարները նախագծենք նրա արգումենտների սեմանտիկորեն իմաստալից սահմանափակումներով (§7.2.4)։ (?)


### Կոնցեպտների վրա հիմնված գերբեռնում

Երբ արդեն ունենք պատշաճ կերպով սահմանված կաղապարներն իրենց ինտերֆեյսներով, կարող ենք գերբեռնում իրականացնե նրանց հատկությունների հիման վրա, ինչպես անում էինք ֆունկցիաների համար։ Դիտարկենք ստանդարտ գրադարանի՝ իտերատորի արժեքը մեծացնող `advance()` ֆունկցիայի (§12.3) մի քիչ պարզեցված տարբերակը։ (?)

````C++
template<Forward_iterator Iter>
void advance(Iter p, int n)        // move p n elements forward
{
    for ( ; −−n; )
        ++p;    // a forward iterator has ++, but not + or +=
}

template<Random_access_iterator Iter, int n>
void advance(Iter p, int n)        // move p n elements forward
{
    p += n;        // a random-access iterator has +=
}
````

Կոմպիլյատորը կընտրի արգումենտների պահանջներին ամենաճիշտը բավարարող կաղապարը։ Այս դեպքում `list` պարունակում է միայն forward? իտերատորները, իսկ `vector`-ը՝ random-access? իտերատորները։ Այսինքն ստանում ենք. (??)

````C++
void user(vector<int>::iterator vip, list<string>::iterator lsp)
{
    advance(vip,10);   // use the fast advance()
    advance(lsp,10);   // use the slow advance()
}
````

Ինչպես և այլ գերբեռնումները, սա նույնպես կոմպիլյացիայի ժամանակի մեխանիզմ է, որը կատարման ժամանակի որևէ բարդություն չի ավելացնում, և եթե կոմպիլյատորը լավագույն տարբերակը չի գտնում՝ ազդարարում է անորոշության սխալի մասին։ Կոնցեպտների վրա հիմնված գեորբեռնման կանոնները շատ ավելի պարզ են քան ընդհանուր գերբեռնման կանոնները (§1.3)։ Նախ դիտարկենք ֆունկցիաների մի քանի տարբերակների միակ արգումենտը. (??)
* Եթե արգումենտը չի համապատասխանում կոնցեպտին, այդ տարբերակը չի կարող ընտրվել։
* Եթե արգումենտը կոնցեպտին համապատասխանում է միայն մեկ տարբերակի համար, ապա հենց այդ տարբերակն էլ ընտրվում է։
* If arguments from two alternatives are equally good matches for a concept, we have an ambiguity.
* If arguments from two alternatives match a concept and one is stricter than the other (match all the requirements of the other and more), that alternative is chosen.

Ընտրվելիք տարբերակը պետք է.
* համապատասխանի բոլոր արգումենտներին, և
* at least an equally good match for all arguments as other alternatives, and
* ունենա գոնե մեկ արգումենտի ավելի լավ համապատասխանություն։


### Վավեր կոդ (?)

Հարցը, թե արդյո՞ք կաղապարի արգումենտների բազմությունն առաջարկում է այն ամենը, ինչ կաղապարը պահանջում է իր պարամետրերով, վերջին հաշվով հանգում է նրան, թե արդյո՞ք ինչ-որ ատրահայտություններ վավեր են։ (?)

`requires`-արտահայտությունների օգտագործմամբ կարող ենք ստուգել արտահայտությունների բազմության վավեր լինելը։ Օրինակ.

````C++
template<Forward_iterator Iter>
void advance(Iter p, int n)        // move p n elements forward
{
    for ( ; −−n; )
        ++p;   // a forward iterator has ++, but not + or +=
}

template<Forward_iterator Iter, int n>
    requires requires(Iter p, int i) { p[i]; p+i; }     // Iter has subscripting and addition
void advance(Iter p, int n)            // move p n elements forward
{
    p += n;           // a random-access iterator has +=
}
````

Ոչ, գրված `requires requires`-ը սխալմունք չէ։ Առաջին `requires`-ը սկսում է պահանջների բլոկը, իսկ երկրորդ `requires`-ը սկսում է `requires(Iter p, int i) { p[i]; p+i; }` արտահայտությունը։

`requires`-արտահայտությունը մի պրեդիկատ է, որը `true` է, եթե նրա մարմնում գրված հրամանները վավեր են, և `false`՝ հակառակ դեպքում։

Ես `requires`-արտահայտությունները դիտարկում եմ որպես ընդհանրացված ծրագրավորման ասեմբլերային կոդ։ Սովորական ասեմբլեր կոդի պես `requires`-արտահայտություններն էլ բավականին ճկուն են և ծրագրավորման որևէ կարգի։ Այս կամ այն կերպ, դրանք ընդհանրացված կոդի ամենաանհետաքրքիր մասերն են, ինչպես ասեմբլերային կոդն է սովորական կոդի ամենաանհետաքրքիր մասը։ Ինչպես և ասեմբլերային կոդը, այնպես էլ `requires`-արտահայտությունները չպետք է երևան «սովորական կոդում»։ Եթե դուք ձեր կոդում հանդիպում եք `requires requires` գիրը, ապա դա հավանաբար շատ ցածր մակարդակի կոդ է։

`advance()`-ում `requires requires` գրառումը դիտավորյալ ոչ էլեգանտ և խրթին է։ Ուշադրություն դարձրեք, որ ես «մոռացել» եմ նշել `+=` գործողությունը և գործողությունների պահանջվող վերադարձրած տիպերը։ (?) Դուք զգուշացվա՛ծ եք եղել։ Առավելություն տվեք անվանված կոնցեպտներին, որոնց անունը արտացոլում է իրենց իմաստը։

Նախապատվություն տվեք ճիշտ սահմանված սեմանտիկայով ճիշտ անվանված կոնցեպտներին (§7.2.4) և դրանց սահմանման մեջ օգտագործեք `requires`-արտահայտությունները։


### Կոնցեպտների սահմանումը

Վերջ ի վերջո, մենք ակնկալում ենք գրադարաններում, ներառյալ ստանդարտ գրադարանը, գտնել օգտակար կոնցեպտներ, ինչպիսիք են `Sequence`—ը և `Arithmetic`-ը։ Ranges Technical Specification-ը [RangesTS] արդեն պարունակում է ստանդարտ գրադարանի ալգորիթմների պահանջներ/սահմանափակումներ (§12.7)։ Այնուամենայնիվ պարզ կոնցեպտները դժվար չէ սահմանել։

Կոնցեպտը կոմպիլյացիայի ժամանակի պրեդիկատ է, որը սահմանում է, թե ինչպես մեկ կամ մի քանի տիպեր կարող են օգտագործվել։ Դիտարկենք պարզ օրինակներից առաջինը.

````C++
template<typename T>
concept Equality_comparable =
    requires (T a, T b) {
        { a == b } −> bool;   // T-երը համեմատել ==-ով
        { a != b } −> bool;   // T-երը համեմատել !=-ով
    };
````

`Equality_comparable` կոնցեպտն օգտագործում ենք, որպեսզի համոզվենք, որ կարող ենք արժեքները համեմատել հավասարության ու անհավասարության իմաստով։ Պարզապես ասում ենք, որ եթե տրված են `T` տիպի երկու արժեքներ, ապա նրանք պետք է համեմատելի լինեն `==` և `!=` գործողություններով, և այդ գործողությունների արդյունք պետք է հնարավոր լինի բերել բուլյան տիպի։ Օրինակ.

````C++
static_assert(Equality_comparable<int>);   // հաջողվում է

struct S { int a; };
static_assert(Equality_comparable<S>);     // ձախողվում է, որովհետև ստրուկտուրաները լռելությամբ == և != չունեն
````

`Equality_comparable` կոնցեպտի սահմանումը ճշգրիտ համապատասխանում է նրա բառացի նկարագրությանը և ոչ ավելին։ `concept`-ի արժեքը միշտ `bool` տիպի է։

Տարատիպ համեմատությունների համար `Equality_comparable`-ի սահմանումը նույնպես շատ պարզ է.

````C++
template<typename T, typename T2 =T>
concept Equality_comparable =
    requires (T a, T2 b) {
        { a == b } −> bool;  // T-ն ==-ով համեմատել T2-ի հետ
        { a != b } −> bool;  // T-ն !=-ով համեմատել T2-ի հետ
        { b == a } −> bool;  // T2-ը ==-ով համեմատել T-ի հետ
        { b != a } −> bool;  // T2-ը !=-ով համեմատել T-ի հետ
    };
````

`typename T2 = T` գրառումն ասում է, որ եթե չնշենք կաղապարի երկրորդ արգումենտը, ապա `T2`-ը կլինի նույնը, ինչ որ `T`-ն։ `T`-ն _կաղապարի լռելության արգումենտ_ է։

`Equality_comparable` կոնցեպտը կարող ենք փորձարկել հետևյալ կերպ.

````C++
static_assert(Equality_comparable<int,double>);  // հաջողվում է
static_assert(Equality_comparable<int>);         // հաջողվում է (T2-ը դառնում է int)
static_assert(Equality_comparable<int,string>);  // ձախողվում է
````

Ավելի բարդ օրինակի համար դիտարկենք հաջորդականությունը.

````C++
template<typename S>
concept Sequence = requires(S a) {
    typename Value_type<S>;             // S-ը պետք է ունենա արժեքի տիպը
    typename Iterator_type<S>;          // S-ը պետք է լինի իտերատոր տիպի

    { begin(a) } −> Iterator_type<S>;   // begin(a)-ն պետք է վերադարձնի իտերատոր
    { end(a) } −> Iterator_type<S>;     // end(a)-ն պետք է վերադարձնի իտերատոր

    requires Same_type<Value_type<S>,Value_type<Iterator_type<S>>>;
    requires Input_iterator<Iterator_type<S>>;
};
````

Որպեսզի `S` տիպը լինի `Sequence`, այն պետք է տրամադրի `Value_type` (իր տարրերի տիպը) և `Iterator_type` (իր իտերատորների տիպը; տես §12.1)։ Այն պետք է նաև երաշխավորի, որ գոյություն ունեն `begin()` և `end()` ֆունկցիաները, որոնք վերադարձնում են իտերատորներ և որոնք հատուկ են ստանդարտ գրադարանի կոնտեյներներին (§11.3)։ Վերջապես, `Iterator_type`-ը իսկապես պետք է լինի `input_iterator`, որի տարրերի տիպը նույնն է, ինչ որ `S`-ի տարրերի տիպը։

Ամենադժվար սահմանվող կոնցերպտները նրանք են, որոնք ներկայացնում են լեզվի հիմնարար սկզբունքները։ Հետևաբար, ավելի լավ է օգտագործել արդեն հաստատված գրադարանի կոնցեպտների բազմությունը (§12.7)։