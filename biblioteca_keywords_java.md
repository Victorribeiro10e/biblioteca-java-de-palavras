# BibliotecaKeywordsJava

Projeto Java: uma biblioteca simples que contém palavras-chave (keywords) de várias linguagens de programação e fornece API para consulta, busca por fragmento e exportação para JSON.

## Estrutura do projeto

```
BibliotecaKeywordsJava/
├── pom.xml
├── README.md
└── src/main/java/com/langlib/
    ├── LanguageKeywords.java
    └── ExampleMain.java
```

---

## pom.xml

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.langlib</groupId>
    <artifactId>biblioteca-keywords</artifactId>
    <version>0.1.0</version>
    <properties>
        <maven.compiler.source>11</maven.compiler.source>
        <maven.compiler.target>11</maven.compiler.target>
    </properties>
    <dependencies>
        <!-- Gson para exportar JSON -->
        <dependency>
            <groupId>com.google.code.gson</groupId>
            <artifactId>gson</artifactId>
            <version>2.10.1</version>
        </dependency>
    </dependencies>
</project>
```

---

## src/main/java/com/langlib/LanguageKeywords.java

```java
package com.langlib;

import com.google.gson.Gson;
import com.google.gson.GsonBuilder;

import java.io.FileWriter;
import java.io.IOException;
import java.util.*;
import java.util.stream.Collectors;

public class LanguageKeywords {
    private final Map<String, Set<String>> data = new HashMap<>();

    public LanguageKeywords() {
        loadDefaults();
    }

    private void loadDefaults() {
        // Java keywords (subset completo pode ser adicionado)
        data.put("java", new HashSet<>(Arrays.asList(
                "abstract","assert","boolean","break","byte","case","catch","char","class","const",
                "continue","default","do","double","else","enum","extends","final","finally","float",
                "for","goto","if","implements","import","instanceof","int","interface","long","native",
                "new","package","private","protected","public","return","short","static","strictfp","super",
                "switch","synchronized","this","throw","throws","transient","try","void","volatile","while"
        )));

        // Python (palavras reservadas comuns)
        data.put("python", new HashSet<>(Arrays.asList(
                "False","None","True","and","as","assert","async","await","break","class","continue","def",
                "del","elif","else","except","finally","for","from","global","if","import","in","is","lambda",
                "nonlocal","not","or","pass","raise","return","try","while","with","yield"
        )));

        // JavaScript (ECMAScript keywords subset)
        data.put("javascript", new HashSet<>(Arrays.asList(
                "await","break","case","catch","class","const","continue","debugger","default","delete","do","else",
                "enum","export","extends","false","finally","for","function","if","import","in","instanceof","let",
                "new","null","return","super","switch","this","throw","true","try","typeof","var","void","while","with","yield"
        )));

        // C / C++ (subset)
        data.put("c", new HashSet<>(Arrays.asList(
                "auto","break","case","char","const","continue","default","do","double","else","enum","extern","float",
                "for","goto","if","inline","int","long","register","restrict","return","short","signed","sizeof","static",
                "struct","switch","typedef","union","unsigned","void","volatile","while","_Alignas","_Alignof","_Atomic","_Bool","_Complex","_Generic","_Imaginary","_Noreturn","_Static_assert","_Thread_local"
        )));

        data.put("cpp", new HashSet<>(Arrays.asList(
                "alignas","alignof","and","and_eq","asm","atomic_cancel","atomic_commit","atomic_noexcept","auto","bitand","bitor",
                "bool","break","case","catch","char","char16_t","char32_t","class","compl","const","constexpr","const_cast",
                "continue","decltype","default","delete","do","double","dynamic_cast","else","enum","explicit","export","extern",
                "false","float","for","friend","goto","if","inline","int","long","mutable","namespace","new","noexcept","not","not_eq",
                "nullptr","operator","or","or_eq","private","protected","public","register","reinterpret_cast","return","short","signed",
                "sizeof","static","static_assert","static_cast","struct","switch","template","this","thread_local","throw","true","try","typedef","typename","union","unsigned","using","virtual","void","volatile","wchar_t","while","xor","xor_eq"
        )));

        // Ruby (subset)
        data.put("ruby", new HashSet<>(Arrays.asList(
                "BEGIN","END","alias","and","begin","break","case","class","def","defined?","do","else","elsif","end","ensure",
                "false","for","if","in","module","next","nil","not","or","redo","rescue","retry","return","self","super","then","true","undef","unless","until","when","while","yield"
        )));

        // HTML (tags and common attributes)
        data.put("html", new HashSet<>(Arrays.asList(
                "html","head","body","title","meta","link","script","style","div","span","p","a","img","ul","ol","li",
                "table","tr","td","th","thead","tbody","footer","header","main","nav","section","article","aside","form",
                "input","button","label","select","option","textarea","h1","h2","h3","h4","h5","h6","doctype","svg"
        )));

        // CSS (at-rules e propriedades comuns)
        data.put("css", new HashSet<>(Arrays.asList(
                "@import","@media","@keyframes","@font-face","color","background","margin","padding","border","display",
                "position","top","right","bottom","left","float","clear","width","height","max-width","min-width","max-height",
                "min-height","font-size","font-family","font-weight","line-height","text-align","vertical-align","flex","grid",
                "align-items","justify-content","gap","opacity","z-index","overflow","transition","transform","animation","visibility"
        )));
    }

    // Adiciona uma nova linguagem com seu conjunto de palavras
    public void addLanguage(String language, Collection<String> keywords) {
        data.put(language.toLowerCase(Locale.ROOT), new HashSet<>(keywords));
    }

    // Retorna keywords de uma linguagem (ou vazio se não existir)
    public Set<String> getKeywords(String language) {
        return data.getOrDefault(language.toLowerCase(Locale.ROOT), Collections.emptySet());
    }

    // Verifica existência de uma keyword em uma linguagem
    public boolean contains(String language, String keyword) {
        return getKeywords(language).contains(keyword);
    }

    // Busca keywords que contenham um fragmento (em todas as linguagens)
    public Map<String, Set<String>> searchByFragment(String fragment) {
        String fragLower = fragment.toLowerCase(Locale.ROOT);
        Map<String, Set<String>> result = new HashMap<>();
        for (Map.Entry<String, Set<String>> e : data.entrySet()) {
            Set<String> matched = e.getValue().stream()
                    .filter(k -> k.toLowerCase(Locale.ROOT).contains(fragLower))
                    .collect(Collectors.toSet());
            if (!matched.isEmpty()) result.put(e.getKey(), matched);
        }
        return result;
    }

    // Exporta todo o dicionário para JSON
    public void exportToJson(String filepath) throws IOException {
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        try (FileWriter writer = new FileWriter(filepath)) {
            gson.toJson(data, writer);
        }
    }
}
```

---

## src/main/java/com/langlib/ExampleMain.java

```java
package com.langlib;

import java.io.IOException;
import java.util.Map;
import java.util.Set;

public class ExampleMain {
    public static void main(String[] args) {
        LanguageKeywords lib = new LanguageKeywords();

        // Exemplo: listar keywords de Java
        Set<String> javaKeys = lib.getKeywords("java");
        System.out.println("Java keywords sample: " + javaKeys.stream().limit(10).toList());

        // Verificar se 'synchronized' é palavra-chave em Java
        System.out.println("Java contains 'synchronized'? " + lib.contains("java", "synchronized"));

        // Buscar fragmento 'class' em todas as linguagens
        Map<String, Set<String>> found = lib.searchByFragment("class");
        System.out.println("Search 'class' => " + found);

        // Exportar para JSON
        try {
            lib.exportToJson("keywords-dictionary.json");
            System.out.println("Exportado para keywords-dictionary.json");
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

---

## README.md (resumo rápido)

```
BibliotecaKeywordsJava

Uma biblioteca Java simples para armazenar e consultar palavras reservadas (keywords) de várias linguagens.

Como usar:
1. Clonar o projeto
2. `mvn package` (requer Maven)
3. Executar `java -cp target/biblioteca-keywords-0.1.0.jar com.langlib.ExampleMain`

APIs principais:
- LanguageKeywords.getKeywords(String language)
- LanguageKeywords.contains(String language, String keyword)
- LanguageKeywords.searchByFragment(String fragment)
- LanguageKeywords.exportToJson(String filepath)

Extensões possíveis:
- Carregar dados de arquivos externos (CSV/JSON)
- Fornecer uma API REST com Spring Boot
- Adicionar mais linguagens e atualizar os conjuntos de palavras
```

---

Se quiser, eu posso:
- Gerar o projeto como um *zip* com os arquivos prontos;
- Adicionar mais linguagens (ex.: PHP, Go, Kotlin, Swift);
- Transformar em uma API REST (Spring Boot) para consultar via HTTP.

Diga qual opção prefere e eu gero o próximo passo.

