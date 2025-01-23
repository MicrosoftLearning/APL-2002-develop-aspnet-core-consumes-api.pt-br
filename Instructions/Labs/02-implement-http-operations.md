---
lab:
  title: 'Exercício: implementar operações HTTP em aplicativos Web do ASP.NET Core Blazor'
  module: 'Module: Implement HTTP operations in ASP.NET Core Blazor Web apps'
---

Neste exercício, você aprenderá a adicionar código a um aplicativo Web do ASP.NET Core Blazor para criar o cliente HTTP e executar as operações `GET`, `POST`, `PUT` e `DELETE`. Esse código é adicionado aos arquivos *.razor.cs* do code-behind. O código para renderizar os dados nos arquivos *.razor* está concluído.

## Objetivos

Após concluir este exercício, você será capaz de:

* Implementar `IHttpClientFactory` como o cliente HTTP
* Implementar operações HTTP em aplicativos Web do ASP.NET Blazor Web

## Pré-requisitos

Para realizar o exercício, você precisará ter as seguintes ferramentas instaladas no sistema:

* [Visual Studio Code](https://code.visualstudio.com)
* [O SDK mais recente do .NET 8.0](https://dotnet.microsoft.com/download/dotnet/8.0)
* [A extensão do C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) para Visual Studio Code

**Tempo estimado para concluir este exercício**: 30 minutos

## Cenário do exercício

Este exercício tem dois componentes:

* Um aplicativo que envia solicitações HTTP para uma API. O aplicativo Web é executado em `http://localhost:5010`.
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

## Implementar código para o cliente HTTP e operações HTTP

O aplicativo Web Fruit exibe os dados de exemplo da API na página inicial e tem a funcionalidade adicionar, editar e excluir. Você precisa adicionar código para implementar as operações do cliente HTTP. 

### Tarefa 1: implementar o cliente HTTP

1. Selecione o arquivo *Program.cs* no painel **Explorer** a fim de abri-lo para edição.

1. Adicione o seguinte código entre os comentários `// Begin HTTP client code` e `// End of HTTP client code`.

    ```csharp
    // Add IHttpClientFactory to the container and set the name of the factory
    // to "FruitAPI". The base address for API requests is also set.
    builder.Services.AddHttpClient("FruitAPI", httpClient =>
    {
        httpClient.BaseAddress = new Uri("http://localhost:5050/");
    });
    ```

1. Salve as alterações em *Program.cs*.

### Tarefa 2: implementar a operação GET

1. Selecione o arquivo *home.razor.cs* no painel **Explorer** para abri-lo para edição. Ele está localizado na pasta `Components/Pages`.

1. Adicione o seguinte código entre os comentários `// Begin GET operation code` e `// End GET operation code`.

    ```csharp
    protected override async Task OnInitializedAsync()
    {
        // Create the HTTP client using the FruitAPI named factory
        var httpClient = HttpClientFactory.CreateClient("FruitAPI");

        // Perform the GET request and store the response. The parameter
        // in GetAsync specifies the endpoint in the API 
        using HttpResponseMessage response = await httpClient.GetAsync("/fruits");

        // If the request is successful deserialize the results into the data model
        if (response.IsSuccessStatusCode)
        {
            using var contentStream = await response.Content.ReadAsStreamAsync();
            _fruitList = await JsonSerializer.DeserializeAsync<IEnumerable<FruitModel>>(contentStream);
        }
        else
        {
            // If the request is unsuccessful, log the error message
            Console.WriteLine($"Failed to load fruit list. Status code: {response.StatusCode}");
        }
    }
    ```

1. Salve as alterações em *Home.razor.cs*.

1. Revise o código no arquivo *Home.razor.cs*. Observe onde o `IHttpClientFactory` é adicionado à página com injeção de dependência.

### Tarefa 3: implementar a operação POST

1. Selecione o arquivo *Add.razor.cs* no painel **Explorer** para abri-lo para edição.

1. Adicione o seguinte código entre os comentários `// Begin POST operation code` e `// End POST operation code`.

    ```csharp
    private async Task Submit()
    {
        // Serialize the information to be added to the database
        var jsonContent = new StringContent(JsonSerializer.Serialize(_fruitList),
            Encoding.UTF8,
            "application/json");

        // Create the HTTP client using the FruitAPI named factory
        var httpClient = HttpClientFactory.CreateClient("FruitAPI");

        // Execute the POST request and store the response. The response will contain the new record's ID
        using HttpResponseMessage response = await httpClient.PostAsync("/fruits", jsonContent);

        // Check if the operation was successful, and navigate to the home page if it was
        if (response.IsSuccessStatusCode)
        {
            NavigationManager?.NavigateTo("/");
        }
        else
        {
            Console.WriteLine("Failed to add fruit. Status code: {response.StatusCode}");
        }
    }
    ```

1. Salve as alterações em *Add.razor.cs* e revise os comentários no código.

### Tarefa 4: implementar a operação PUT

1. Selecione o arquivo *Edit.razor.cs* no painel **Explorer** a fim de abri-lo para edição.

1. Adicione o seguinte código entre os comentários `// Begin PUT operation code` e `// End PUT operation code`.

    ```csharp
    private async Task Submit()
    {
        // Create the HTTP client using the FruitAPI named factory
        var httpClient = HttpClientFactory.CreateClient("FruitAPI");

        // Store the updated data in a JSON object
        var jsonContent = new StringContent(JsonSerializer.Serialize(_fruitList), 
            Encoding.UTF8, "application/json");

        // Execute the PUT request
        using HttpResponseMessage response = await httpClient.PutAsync($"/fruits/{Id}", jsonContent);

        // If the response is successful, navigate back to the home page 
        if (response.IsSuccessStatusCode)
        {
            NavigationManager?.NavigateTo("/");
        }
        else
        {
            Console.WriteLine("Failed to update fruit with edits. Status code: {response.StatusCode}");
        }
    }
    ```

1. Salve as alterações em *Edit.razor.cs* e revise os comentários no código.

### Tarefa 5: implementar a operação DELETE

1. Selecione o arquivo *Delete.razor.cs* no painel **Explorer** a fim de abri-lo para edição.

1. Adicione o seguinte código entre os comentários `// Begin DELETE operation code` e `// End DELETE operation code`.

    ```csharp
    private async Task Submit()
    {
        // Create the HTTP client using the FruitAPI named factory
        var httpClient = HttpClientFactory.CreateClient("FruitAPI");

        // Execute the DELETE request and store the response
        using HttpResponseMessage response = await httpClient.DeleteAsync("/fruits/" + Id.ToString());

        // Return to the home page 
        if (response.IsSuccessStatusCode)
        {
            NavigationManager?.NavigateTo("/");
        }
        else
        {
            Console.WriteLine("Failed to delete fruit. Status code: {response.StatusCode}");
        }
    }
    ```

1. Salve as alterações em *Delete.razor.cs* e revise os comentários no código.

## Executar e testar o aplicativo Web

### Tarefa 1: Executar o aplicativo Web

1. No menu superior do Visual Studio Code, selecione **Executar \| Iniciar depuração** ou selecione **F5**. Depois que o projeto for concluído, uma janela do navegador deve ser iniciada com o aplicativo Web em execução e exibindo os dados de exemplo da API, conforme mostrado na captura de tela a seguir.

    ![Captura de tela do aplicativo Web exibindo os dados de exemplo.](media/02-web-app-get-sample-data.png)

    >**Observação:** é possível ignorar com segurança o prompt abaixo se aparecer quando você executar o aplicativo.

    ![Captura de tela do prompt para instalar um certificado autoassinado.](media/install-cert.png)

### Tarefa 1: Testar o aplicativo Web

1. Selecione o botão **Adicionar à lista** e preencha o formulário gerado. Em seguida, selecione o botão **Criar**.

1. Verifique se a adição aparece na parte inferior da lista.

1. Escolha um item na lista para editar e selecione o botão **Editar**. 
1. Edite o **Fruit Name** e o campo **Available?**. Em seguida, selecione **Atualizar**.

1. Verifique se as alterações aparecem na lista. 

1. Escolha um item na lista para excluir e selecione o botão **Excluir**.

1. Na página Excluir, verifique se o item selecionado é exibido e clique no botão **Excluir**.

1. Verifique se o item não aparece mais na lista.

Quando tudo estiver pronto para terminar o exercício:

* Feche o navegador ou a guia do navegador e, no Visual Studio Code, selecione **Executar \| Parar depuração** ou **Shift + F5**. 

* Pare a API Fruit digitando **Ctrl + C** no terminal em que ela está sendo executada.

## Revisão

Neste exercício você aprendeu a:

* Implementar `IHttpClientFactory` como o cliente HTTP
* Implementar operações HTTP nos arquivos code-behind do ASP.NET Core Blazor
