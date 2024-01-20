---
lab:
  title: 'Exercício: implementar operações HTTP do Razor Pages no ASP.NET Core'
  module: 'Module: Implement HTTP operations in ASP.NET Core Razor Pages'
---

Neste exercício, você aprenderá a adicionar o código necessário a um aplicativo do Razor Pages no ASP.NET Core para criar o cliente HTTP e executar as operações `GET`, `POST`, `PUT` e `DELETE`. Esse código é adicionado aos arquivos *.cshtml.cs* do code-behind. O código para renderizar os dados nos arquivos *.cshtml* está concluído.

## Objetivos

Após concluir este exercício, você será capaz de:

* Implementar `IHttpClientFactory` como o cliente HTTP
* Implementar operações HTTP do Razor Pages no ASP.NET Core

## Pré-requisitos

Para realizar o exercício, você precisará ter as seguintes ferramentas instaladas no sistema:

* [Visual Studio Code](https://code.visualstudio.com)
* [O SDK mais recente do .NET 7.0](https://dotnet.microsoft.com/download/dotnet/7.0)
* [A extensão do C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) para Visual Studio Code

**Tempo estimado para concluir este exercício**: 30 minutos

## Cenário do exercício

Este exercício tem dois componentes:

* Um aplicativo que envia solicitações HTTP para uma API. O aplicativo é executado em `http://localhost:5010`
* Uma API que responde a solicitações HTTP. A API é executada em `http://localhost:5050`

![Decorativa](media/02-architecture.png)


## Baixar o código

Nesta seção, você baixará o código do aplicativo Web Fruit e da API Fruit. Você também executará a API Fruit localmente para que ela esteja disponível para o aplicativo Web.

### Tarefa 1: baixar e executar o código da API

1. Clique com o botão direito do mouse no link a seguir e selecione a opção **Salvar link como**. 

    * Código [do projeto FruitAPI](https://raw.githubusercontent.com/MicrosoftLearning/APL-2002-develop-aspnet-core-consumes-api/master/Allfiles/Downloads/FruitAPI.zip)

1. Inicie o **Explorador de Arquivos** e vá até o local em que o arquivo foi salvo.

1. Descompacte o arquivo em sua própria pasta.

1. Abra o **Terminal do Windows** ou um **Prompt de Comando** e vá até o local em que você extraiu o código da API.

1. Em uma janela do **Terminal do Windows**, execute o seguinte comando do `dotnet`:

    ```
    dotnet run
    ```

1. Confira a seguir um exemplo do resultado gerado. Observe a linha `Now listening on: http://localhost:5050` na saída. Ela identifica o host e a porta da API.

    ```
    info: Microsoft.EntityFrameworkCore.Update[30100]
          Saved 3 entities to in-memory store.
    info: Microsoft.Hosting.Lifetime[14]
          Now listening on: http://localhost:5050
    info: Microsoft.Hosting.Lifetime[0]
          Application started. Press Ctrl+C to shut down.
    info: Microsoft.Hosting.Lifetime[0]
          Hosting environment: Development
    info: Microsoft.Hosting.Lifetime[0]
          Content root path: 
          <project location>
    ```

>**Observação:** deixe a API Fruit em execução durante todo o resto do exercício. 

### Tarefa 2: baixar e abrir o projeto de aplicativo Web

1. Clique com o botão direito do mouse no link a seguir e selecione a opção **Salvar link como**. 

    * [Código de projeto code-behind do aplicativo Web Fruit](https://raw.githubusercontent.com/MicrosoftLearning/APL-2002-develop-aspnet-core-consumes-api/master/Allfiles/Downloads/FruitWebApp-codebehind.zip)

1. Inicie o **Explorador de Arquivos** e vá até o local em que o arquivo foi salvo.

1. Descompacte o arquivo em sua própria pasta.

1. Inicie o Visual Studio Code e selecione **Arquivo**. Em seguida, clique em **Abrir Pasta...** na barra de menus.

1. Vá até o local onde você descompactou os arquivos do projeto e selecione a pasta *FruitWebApp-codebehind*.

1. A estrutura do projeto no painel **Explorer** deve ser semelhante à captura de tela a seguir. Se o painel **Explorer** não estiver visível, selecione **Visualizar** e, em seguida, selecione **Explorer** na barra de menus.

    ![Captura de tela mostrando a estrutura do projeto do aplicativo Web Fruit.](media/02-web-app-cb-struture.png)

>**Observação:** reserve um tempo para revisar o código em cada um dos arquivos editados durante o exercício. O código é muito comentado e pode ajudar você a entender a base do código.

## Implementar código para o cliente HTTP e a operação `GET`

O aplicativo Web Fruit exibe os dados de exemplo da API na home page. Você precisa adicionar código a fim de implementar o cliente HTTP e a operação `GET`. Assim, o aplicativo Web pode ter dados para exibir na home page ao compilá-lo e executá-lo pela primeira vez.

### Tarefa 1: implementar o cliente HTTP

1. Selecione o arquivo *Program.cs* no painel **Explorer** a fim de abri-lo para edição.

1. Adicione o seguinte código entre os comentários `// Begin HTTP client code` e `// End of HTTP client code`.

    ```csharp
    // Add IHttpClientFactory to the container and set the name of the factory
    // to "FruitAPI". The base address for API requests is also set.
    builder.Services.AddHttpClient("FruitAPI", httpClient =>
    {
        httpClient.BaseAddress = new Uri("http://localhost:5050/fruitlist/");
    });
    ```

1. Salve as alterações em *Program.cs*.

### Tarefa 2: implementar a operação `GET`

1. Selecione o arquivo *Index.cshtml.cs* no painel **Explorer** a fim de abri-lo para edição.

1. Adicione o seguinte código entre os comentários `// Begin GET operation code` e `// End GET operation code`.

    ```csharp
    // OnGet() is async since HTTP requests should be performed async
      public async Task OnGet()
      {
          // Create the HTTP client using the FruitAPI named factory
          var httpClient = _httpClientFactory.CreateClient("FruitAPI");

          // Perform the GET request and store the response. The empty parameter
          // in GetAsync doesn't modify the base address set in the client factory 
          using HttpResponseMessage response = await httpClient.GetAsync("");

          // If the request is successful deserialize the results into the data model
          if (response.IsSuccessStatusCode)
          {
              using var contentStream = await response.Content.ReadAsStreamAsync();
              FruitModels = await JsonSerializer.DeserializeAsync<IEnumerable<FruitModel>>(contentStream);
          }
      }
    ```

1. Salve as alterações em *Index.cshtml.cs*.

1. Analise o código no arquivo *Index.cshtml.cs*. Observe onde o `IHttpClientFactory` é adicionado à página com injeção de dependência. Observe também que o modelo de dados é vinculado à página usando o atributo `[BindProperty]`.

### Etapa 9: executar o aplicativo Web

1. No menu superior do Visual Studio Code, selecione **Executar \| Iniciar depuração** ou selecione **F5**. Depois que o projeto for concluído, uma janela do navegador deve ser iniciada com o aplicativo Web em execução e exibindo os dados de exemplo da API, conforme mostrado na captura de tela a seguir.

    ![Captura de tela do aplicativo Web exibindo os dados de exemplo.](media/02-web-app-get-sample-data.png)

    >**Observação:** posteriormente no exercício, você adicionará um código para habilitar a funcionalidade de adicionar, editar e excluir do aplicativo Web. 

    >**Observação:** é possível ignorar com segurança o prompt abaixo se aparecer quando você executar o aplicativo.

    ![Captura de tela do prompt para instalar um certificado autoassinado.](media/install-cert.png)

1. Para continuar com o exercício, feche o navegador ou a guia do navegador e, no Visual Studio Code, selecione **Executar \| Parar depuração** ou **Shift + F5**.

## Implementar código para as operações `POST`, `PUT` e `DELETE`

Nesta seção, você adicionará um código ao projeto para habilitar as funcionalidades **Adicionar à lista**, **Editar** e **Excluir** no aplicativo Web. 

### Tarefa 1: implementar a operação `POST`

1. Selecione o arquivo *Add.cshtml.cs* no painel **Explorer** a fim de abri-lo para edição.

1. Adicione o seguinte código entre os comentários `// Begin POST operation code` e `// End POST operation code`.

    ```csharp
    public async Task<IActionResult> OnPost()
    {
        // Serialize the information to be added to the database
        var jsonContent = new StringContent(JsonSerializer.Serialize(FruitModels),
            Encoding.UTF8,
            "application/json");
    
        // Create the HTTP client using the FruitAPI named factory
        var httpClient = _httpClientFactory.CreateClient("FruitAPI");
    
        // Execute the POST request and store the response. The parameters in PostAsync 
        // direct the POST to use the base address and passes the serialized data to the API
        using HttpResponseMessage response = await httpClient.PostAsync("", jsonContent);
    
        // Return to the home (Index) page and add a temporary success/failure 
        // message to the page.
        if (response.IsSuccessStatusCode)
        {
            TempData["success"] = "Data was added successfully.";
            return RedirectToPage("Index");
        }
        else
        {
            TempData["failure"] = "Operation was not successful";
            return RedirectToPage("Index");
        }
    }
    ```

1. Salve as alterações em *Add.cshtml.cs* e revise os comentários no código.

1. No menu superior do Visual Studio Code, selecione **Executar \| Iniciar depuração** ou selecione **F5**. Depois que o projeto for concluído, uma janela do navegador deve ser iniciada com o aplicativo Web em execução

1. Selecione o botão **Adicionar à lista** e preencha o formulário gerado. Em seguida, selecione o botão **Criar**.

1. Verifique se a adição aparece na parte inferior da lista. A mensagem de êxito/falha na parte superior da página mostrará se houve um problema.

1. Para continuar com o exercício, feche o navegador ou a guia do navegador e, no Visual Studio Code, selecione **Executar \| Parar depuração** ou **Shift + F5**.

### Tarefa 1: implementar a operação `PUT`

1. Selecione o arquivo *Edit.cshtml.cs* no painel **Explorer** a fim de abri-lo para edição.

1. Adicione o seguinte código entre os comentários `// Begin PUT operation code` e `// End PUT operation code`.

    ```csharp
    public async Task<IActionResult> OnPost()
        {
            // Serialize the information to be edited in the database
            var jsonContent = new StringContent(JsonSerializer.Serialize(FruitModels),
                Encoding.UTF8,
                "application/json");
    
            // Create the HTTP client using the FruitAPI named factory
            var httpClient = _httpClientFactory.CreateClient("FruitAPI");
    
            // Execute the PUT request and store the response. The parameters in PutAsync 
            // appends the item Id to the base address and passes the serialized data to the API
            using HttpResponseMessage response = await httpClient.PutAsync(FruitModels.id.ToString(), jsonContent);
    
            // Return to the home (Index) page and add a temporary success/failure 
            // message to the page.
            if (response.IsSuccessStatusCode)
            {
                TempData["success"] = "Data was edited successfully.";
                return RedirectToPage("Index");
            }
            else
            {
                TempData["failure"] = "Operation was not successful";
                return RedirectToPage("Index");
            }
    
        }
    ```

1. Salve as alterações em *Edit.cshtml.cs* e revise os comentários no código.

1. No menu superior do Visual Studio Code, selecione **Executar \| Iniciar depuração** ou selecione **F5**. Depois que o projeto for concluído, uma janela do navegador deve ser iniciada com o aplicativo Web em execução

1. Escolha um item na lista para editar e selecione o botão **Editar**. 
1. Edite o **Fruit Name** e o campo **Available?**. Em seguida, selecione **Atualizar**.

1. Verifique se as alterações aparecem na lista. A mensagem de êxito/falha na parte superior da página mostrará se houve um problema.

1. Para continuar com o exercício, feche o navegador ou a guia do navegador e, no Visual Studio Code, selecione **Executar \| Parar depuração** ou **Shift + F5**.

### Tarefa 1: implementar a operação `DELETE`

1. Selecione o arquivo *Delete.cshtml.cs* no painel **Explorer** a fim de abri-lo para edição.

1. Adicione o seguinte código entre os comentários `// Begin DELETE operation code` e `// End DELETE operation code`.

    ```csharp
    public async Task<IActionResult> OnPost()
    {
        // Create the HTTP client using the FruitAPI named factory
        var httpClient = _httpClientFactory.CreateClient("FruitAPI");
    
        // Appends the data Id for deletion to the base address and performs the operation
        using HttpResponseMessage response = await httpClient.DeleteAsync(FruitModels.id.ToString());
    
        // Return to the home (Index) page and add a temporary success/failure 
        // message to the page.
        if (response.IsSuccessStatusCode)
        {
            TempData["success"] = "Data was deleted successfully.";
            return RedirectToPage("Index");
        }
        else
        {
            TempData["failure"] = "Operation was not successful";
            return RedirectToPage("Index");
        }
    
    }
    ```

1. Salve as alterações em *Delete.cshtml.cs* e revise os comentários no código.

1. No menu superior do Visual Studio Code, selecione **Executar \| Iniciar depuração** ou selecione **F5**. Depois que o projeto for concluído, uma janela do navegador deve ser iniciada com o aplicativo Web em execução

1. Escolha um item na lista para excluir e selecione o botão **Excluir**. 

1. Edite o **Fruit Name** e o campo **Available?**. Em seguida, selecione **Atualizar**.

1. Verifique se o item não aparece mais na lista. A mensagem de êxito/falha na parte superior da página mostrará se houve um problema.

Quando tudo estiver pronto para terminar o exercício:

* Feche o navegador ou a guia do navegador. No Visual Studio Code, selecione **Executar \| Parar depuração** ou **Shift + F5**. 

* Pare a API Fruit inserindo `Ctrl + C` no terminal em que está sendo executada.

## Revisão

Neste exercício você aprendeu a:

* Implementar `IHttpClientFactory` como o cliente HTTP
* Implementar operações HTTP dos arquivos code-behind do Razor Pages no ASP.NET Core
