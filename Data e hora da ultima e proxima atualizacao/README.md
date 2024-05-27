# Exibir a data da próxima atualização em seu Dashboard no Power BI

Olá, este é um tutorial de como criar uma medida que mostre a data e hora da próxima atualização do seu dashboard.

Seguindo este passo a passo, você poderá informar aos usuários a hora exata em que os dados serão atualizados novamente, proporcionando mais transparência e previsibilidade.

Passo a Passo:

## 1. Criação da Tabela de Agendamentos:

Primeiramente, crie uma tabela contendo as datas e horários das primeiras atualizações programadas.

A data pode ser a data de criação do dashboard. As horas devem seguir o agendamento feito no Power BI Serviço.

Essa tabela é essencial para termos um ponto de referência para calcular a próxima atualização.

	Agendamentos = 
	DATATABLE(
			"DataHora", DATETIME,
	    {
	        {"2024-05-24 08:00:00"},
	        {"2024-05-24 10:00:00"},
	        {"2024-05-24 12:00:00"},
	        {"2024-05-24 14:00:00"},
	        {"2024-05-24 16:00:00"}
	    }
	)

Você terá a seguinte tabela

![image](https://github.com/glyninsights/Power-BI/assets/162929817/ccaa5ec3-fb06-4c8b-b16f-8600cfedae57)


## 2. Criação da medida para Próxima Atualização:

Em seguida, crie a medida que calcula a próxima atualização com base na data e hora atuais.

	ProximaAtualizacao = 
	// Captura a data e hora atuais
	var DataHoraAtual = NOW()
	var DataAtual = DATEVALUE(DataHoraAtual)
	var HoraAtual = TIMEVALUE(DataHoraAtual)
	
	// Filtra os agendamentos que ainda não ocorreram
	var AgendamentoInicial = 
	FILTER(
	    Agendamentos,
	    [DataHora] > DataHoraAtual
	)
	
	// Filtra e adiciona a coluna "Hoje" para agendamentos que ocorrerão hoje após a hora atual
	var AgendamentoHoje = 
	ADDCOLUMNS(
	    FILTER(
	        Agendamentos,
	        TIMEVALUE([DataHora]) > HoraAtual
	    ),
	    "Hoje",
	    DataAtual + TIMEVALUE(Agendamentos[DataHora])
	)
	
	// Adiciona a coluna "Amanha" para agendamentos que ocorrerão amanhã
	var AgendamentoAmanha = 
	ADDCOLUMNS(
	    Agendamentos,
	    "Amanha",
	    DataAtual + 1 + TIMEVALUE(Agendamentos[DataHora])
	)
	
	// Encontra a próxima atualização inicial
	var ProximaAtualizacaoInicio = MINX(AgendamentoInicial, [DataHora])
	// Encontra a próxima atualização para hoje
	var ProximaAtualizacaoHoje = MINX(AgendamentoHoje, [Hoje])
	// Encontra a próxima atualização para amanhã
	var ProximaAtualizacaoAmanha = MINX(AgendamentoAmanha, [Amanha])
	
	// Determina a próxima atualização com base nas condições
	var ProximaAtualizacao = 
	SWITCH(
	    TRUE(),
	    DataHoraAtual <= ProximaAtualizacaoInicio, ProximaAtualizacaoInicio,
	    DataHoraAtual > ProximaAtualizacaoInicio && DataHoraAtual <= ProximaAtualizacaoHoje, ProximaAtualizacaoHoje,
	    ProximaAtualizacaoAmanha
	)
	
	// Retorna a próxima atualização como texto
	return "Próxima atualização: " & ProximaAtualizacao

## 3. Adicionando o Visual de Cartão:

Finalmente, adicione um visual de Cartão ao seu dashboard e insira a medida que você acabou de criar.

Isso exibirá a data e hora da próxima atualização diretamente no seu dashboard, facilitando o acompanhamento pelos usuários.

Exemplo:

![image](https://github.com/glyninsights/Power-BI/assets/162929817/9c4e6756-867b-4252-8a4e-12ed70c75b86)


Benefícios:

🔹 Transparência: Usuários sabem exatamente quando esperar dados atualizados.

🔹 Confiança: Aumenta a confiança no dashboard, mostrando que ele é atualizado regularmente.

🔹 Eficiência: Reduz as dúvidas e perguntas sobre a próxima atualização dos dados.

Experimente esta abordagem e melhore a experiência dos usuários do seu dashboard no Power BI! Se você tiver dúvidas ou quiser compartilhar suas próprias experiências, deixe um comentário abaixo. 🚀📊
