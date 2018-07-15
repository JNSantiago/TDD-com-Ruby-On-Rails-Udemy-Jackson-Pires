#### Tips and Tricks of TDD and BDD
* Executar todos os teste
$ rspec
* Executar um arquivo específico de testes:
$ rspec spec/calculator/calculator_spec.rb

* Executar apenas um teste específico de um arquivo
$ rspec spec/calculator/calculator_spec.rb -e 'something test'

* Executar um teste indicando a linha
$ rspec spec/calculator/calcuator_spec.rb:15

##### CONTEXT
context = agrupa um ou mais testes em comum

```ruby
context 'group of tests' do
    it 'something test' do
    end
    it 'another related test' do
    end
end
```

##### SUBJECT
subject = instancia o objeto da classe informada no describe

```ruby
# opcional caso queira trocar o nome do subject
subject(:calc) { described_class.new() }
```

1) Subject Implícito
```ruby
describe Calculator do
    it 'with positive numbers' do
        result = subject.sum(5, 7)
        expect(result).to eq(12)
    end
end
```
2) Subject Explícito
```ruby
# opcional caso queira trocar o nome do subject
subject(:calc) { described_class.new() }

describe Calculator do
    it 'with positive numbers' do
        result = calc.sum(5, 7) # muda o nome do objeto no subject
        expect(result).to eq(12)
    end
end
```
##### Expect
In line expect # no inline expect, o subject fica implicito.
```ruby
it { is_expected.to cover(2)  }
```
Expect com condicionais:
```ruby
it { is_expected.to start_with('ruby').and end_with('rails')  }
it { expect(['laranja', 'banana']).to eq('laranja').or eq('banana')  }
```

##### it, xit and other things
1) it = bloco do teste
2) xit = acrescentando o x antes do it o método do teste fica pendente

##### Matchers
Matchers são usados com o expet(...).to e com o expect(...).not_to

1) Matchers de Igualdade
1.1) equal ou be = Testa se os OBJETOS são iguais, no exemplo abaixo x NÃO é igual a y, pois são objetos diferentes.
```ruby
it '#equal' do
    x = 'ruby'
    y = 'ruby'
    expect(x).to equal(y)
end
```
1.2) eql ou eq = Testa o VALOR, no teste abaixo x É igual a y, pois ele compara os valores:
```ruby
expect(x).to eql(y)
```

2) Matchers de Verdadeiro/Falso
2.1) be true / be_trufhy
```ruby
expect(1.odd?).to be true
```
2.2) be false / be_falsey
```ruby
expect(1.even?).to be false
```
2.3) be_nil = verifica se a variavel está vazia
```ruby
expect(defined? x).to be_nil
```
3) Matchers de igualdade
3.1) >
```ruby
expect(5).to be > 1
```
3.2) >=
```ruby
expect(5).to be >= 1
```
3.3) <
```ruby
expect(5).to be < 10
```
3.4) <=
```ruby
expect(5).to be <= 10
```
3.5)be_between(min, max).inclusive
No exemplo abaixo, verifica se 5 está entre 2 e 7, inclusive o 2 e o 7
```ruby
expect(5).to be_between(2, 7).inclusive
```
3.6)be_between(min, max).exclusive
```ruby
expect(5).to be(2,7).exclusive
```
3.7)match(/regex/)
```ruby
expect("fulano@com.br").to match(/..@../)
```
3.8)start_with
```ruby
expect("fulano de tal").to start_with('fulano')
expect([1,2,3]).to start_with(1)
```
3.9)end_with
```ruby
expect("fulano de tal").to end_with('tal')
```
4) Classes e Tipos
4.1)be_instance_of # verifica exatamente o tipo da classe
```ruby
expect(10).to be_instance_of(Integer)
```
4.2)be_kind_of # semelhante ao be_instance_of mas pode ser por herança
```ruby
expect(10).to be_kind_of(Integer)
```
4.3)respond_to
```ruby
expect("ruby").to respond_to(:size)
```
5) Matchers para atributos de classe
5.1) have_attributes
```ruby
pessoa = Pessoa.new
expect(pessoa).to have_attributes(:nome)
expect(pessoa).to have_attributes(idade: (be >= 20))
```
6) Matchers Predicados = Funciona para qualquer método predicado do ruby, basta colocar o prefixo be seguido do método sem a ?.
```ruby
expect(1).to be_odd
expect(0).to be_zero
expect(x).to be_nil
expect(x).to be_empty
```
7) Matchers de erros
7.1) raise_exception
```ruby
expect{subject.div(3,0)}.to raise_exception
```
7.2) raise_error
```ruby
expect{subject.div(3.0)}.to raise_error
```
8) Matchers para arrays

8.1) include
```ruby
array = [1, 2, 3]
expect(array).to include(2)
expect(array).to include(3, 2)
```
8.2) match_array
```ruby
expect(array).to match_array([1, 2, 3])
```
8.3) contain_exactly
```ruby
expect(array).to contain_exactly(1, 2, 3)
```
9) Matchers para ranges
considerando subject com o range (1..5)
```ruby
expect(subject).to cover(2)
expect(subject).to cover(2,5)
expect(subject).not_to cover(0,6)
```
10) Matcher para coleções
no exemplo abaixo ele espera que todos os números da coleção sejam impar
```ruby
expect([1,7,9]).to all (be_odd)
expect([1,7,9]).to all (be_odd).and be_an(Integer)
```
11) Matcher satisfy
```ruby
expect(10).to satisfy { |x| x % 2 == 0 }
expext(9).to satisfy('be a multiple of 3') do |x|
	x % 3 == 0	
end
```
12) Matcher change = verifica se o valor de uma determinada propriedade mudou
```ruby
it { expect{Contador.incrementa}.to change { Contador.qtd } }
it { expect{Contador.incrementa}.to change { Contador.qtd }.by(1) }
it { expect{Contador.incrementa}.to change { Contador.qtd }.from(2).to(3) }
```
13) Matcher output = testar as saídas do sistema, similar ao puts

13.1) Mensagem para a saída padrão
```ruby
it { expect(puts "joao").to output.to_stdout }
it { expect(print "joao").to output("joao").to_stdout  }
```
13.2) Mensagem para a saída de erros
```ruby
it { expect(warn "joao").to output.to_stderr }
```
14) Negativando Matchers
```ruby
Rspec::Matchers.define_negated_matcher :<novo>, :<velho>
```
15) Customizando Matchers
Criar um custom_spec.rb
```ruby
RSpec::Matchers.define :be_a_multiple_of do |expected|
	match do |actual|
		actual % expected == 0
	end

	failure_message do |actual|
		"expected that #{actual} would be a multiple of #{expected}"
	end

	description do
		"be a multiple of #{expected}"
	end
end

describe 18, 'Custom Matcher' do
	it { is_expected.to be_a_multiple_of(3)}
end
```
##### Helper Methods
1) Arbitrários e de Módulo
```ruby
describe 'Ruby on Rails' do
	it { expected(fruta).to eq('banana')  }
	
	# Método arbitrário
	def fruta
		%w(banana laranja uva)
	end
end
```
Ou voce pode isolar o método em um modulo

criar arquivo helper.rb
```ruby
module Helper
	def fruta
		%w(banana laranja uva)
	end
end
```
No arquivo de teste arquivo.spec.rb
```ruby
require_relative 'helper'

# Necessário usar o configure para usar o método
RSpec.configure do |conf|
	conf.include Helper
end

describe 'Ruby on Rails' do
	it { expected(fruta).to eq('banana')  }
end
```
2) let e let!

let = Quando você precisa atribuir uma variável, ao invés de usar um bloco before para criar uma variável de instância, use let. Ao usar let, a variável é carregada apenas quando ela é utilizada pela primeira vez no teste e fica na cache até o teste em questão terminar.
```ruby
let(:pessoa) { Pessoa.new  }
```
let! = Força a invocação do método/helper antes de cada teste

##### Hooks
1) before e after
```ruby
before(:suite) # antes de toda a suite de testes
after(:suite) # depois de toda a suíte de testes

before(:all) # antes de todos os testes
before(:context)
after(:all) # depois de todos os testes
after(:context)
```
As configurações dos Hooks são feitas no arquivo spec_helper.rb
```ruby
config.before(:suite) do
	puts ">>> Antes da Suite de testes"
end

config.after(:suite) do
	puts ">>> Depois da Suite de testes"
end
```
2) around = faz de uma vez só o after e o before
```ruby
around(:each) do |teste|
	puts "ANTES"
	@pessoa = Pessoa.new

	teste.run
	
	puts "Depois"
	@pessoa.nome = "joao"
end
```
##### Agregando falhas
Podemos permitir a visualização do erro de todos os testes quando ouver mais de um teste
```ruby
agregate_failures do
	expect(5).to be_between(2,7).inclusive
	expect(1).to be_between(2,7).inclusive
	expect(7).to be_between(2,7).inclusive
end
```
ou
```ruby
it 'falhas agregadas', :aggregate_failures do
	expect(5).to be_between(2,7).inclusive
	expect(1).to be_between(2,7).inclusive
	expect(7).to be_between(2,7).inclusive
end
```
##### Shared Examples
Consiste em criar um teste genérico e dinâmico, afim de diminuir o código, no exemplo abaixo são testadas três funções usando um único teste
```ruby
class Pessoa
	attr_reader :status

	def feliz
		@pessoa.status = 'Sentindo-se Feliz!'
	end

	def triste
		@pessoa.status = 'Sentindo-se Triste!'
	end

	def contente
		@pessoa.status = 'Sentindo-se Contente!'
	end
end
require 'pessoa'

shared_examples 'status' do |sentimento|
	it "#{sentimento}" do
		pessoa.send("#{sentimento}!")
		expect(pessoa.status).to eq("Sentindo-se #{sentimento.capitalize}!")
	end
end

describe 'Pessoa' do
	subject(:pessoa) { Pessoa.new }

	# Escolha uma das três sintaxes
	include_examples 'status', :feliz
	it_behaves_like 'status', :feliz
	it_should_behave_like 'status', :feliz

	include_examples 'status', :triste
	include_examples 'status', :contente
end
```
##### Tag Filter
É possível também dar um nome à suite de testes e executá-los chamando por esse nome por meio do comando:
```
$ rspec -t collection
```

```ruby
describe Array.new([1,2,3]), "Array", type: 'collection' do
	it '#include' do
	  expect(subject).to include(2)
	  expect(subject).to include(2,1)
	end
end
```
===================================================

======== Test Double ==============================
Dublê de teste é um termo genérico para qualquer objeto falso, utilizado no lugar de um objeto real, para propósitos de testes.
Usamos o método double para criar um dublê. Em outras palavras, um dublê age como um objeto Ruby, que pode ou não aceitar "mensagens" (métodos). Dublês são 'rigorosos'/strict, ou seja, você precisa indicar quais mensagens ele aceita.

```ruby
it '==' do
	user = double('User')
	allow(user).to receive_messages(name: 'jack', password: 'secret')
	puts user.name
	puts user.password
end
```
1) Método "As Null Object"
Qualquer chamada ao dublê que retorne qualquer mensagem de erro ele retorna null
```ruby
it '==' do
	user = double('User').as_null_object
	allow(user).to receive_messages(name: 'jack', password: 'secret')
	puts user.name
	puts user.password
	puts user.abc # abc não é um atributo permitido, então retornaria um erro
end
```
##### Stubs
Um stub nada mais é do que forçar uma resposta específica para um determinado método de um objeto colaborador
Neste exemplo forçamos has_finished a retornar true

Stubs são para a fase de Setup, e são usados para substituir estados.
```ruby
class Student
	def has_finished?(course)
		# retorna true ou false
	end
end

class Course
	def complete?
		# retorna true ou false
	end
end
```
arquivo stub_spec.rb
```ruby
require 'student'
require 'course'

describe 'Stub' do
  it '#has_finished?' do
    student = Student.new
    course = Course.new

    allow(student).to receive(:has_finished?)
      .with(an_instance_of(Course))
      .and_return(true)

    course_finished = student.has_finished?(course)

    expect(course_finished).to be_truthy
  end
end
```
1) Method Stubs

1.1) Restrição de argumentos (with)
```ruby
allow(student).to receive(:has_finished?)
      .with(an_instance_of(Course))
	  .and_return(true)
```
1.2) Argumentos dinâmicos
```ruby
it 'Argumentos Dinâmicos' do
    student = Student.new

    allow(student).to receive(:foo) do |arg|
      if arg == :hello
        "olá"
      elsif arg == :hi
        "Hi!!!"
      end
    end

    expect(student.foo(:hello)).to eq("olá")
    expect(student.foo(:hi)).to eq("Hi!!!")
end
```
##### Mocks
Os mocks são para a fase de Verify, e são usados para testar comportamentos.

# fase de setup
```ruby
student = Student.new
# fase de verificação
expect(student).to receive(:bar)
# fase de exercise
student.bar
```
1) Mock Expectations
1.1) Mock com contagem de mensagens

1.1.1) one = chamar o método uma vez
```ruby
student = Student.new
expect(student).to receive(:foo).with(123)
student.foo(123)
```
1.1.2) twice = chamar duas vezes
```ruby
student = Student.new
expect(student).to receive(:foo).with(123).twice
student.foo(123)
student.foo(123)
```
1.1.3) exactly(3).times = exatamente três vezes
...

1.1.4) al_least(:once) = pelo menos uma vez
...

1.1.5) al_least(:twice) = pelo menos duas vezes
...

1.1.6) al_least(n).times
....

1.2) Mock com valor de retorno = define um valor padrão para retorno
```ruby
student = Student.new
expect(student).to receive(:foo).with(123).and_return(true)
student.foo(123)
```
##### Configurar o rspec no rails
1) Gerar um projeto sem os testes padrão
# o -T "pula" a geração dos testes, o rails por padrão usa o minitest
```
$ rails new test_app -T
```
2) Inserir o rspec no GemFile
```ruby
# a gem capybara é OBRIGATÓRIA
group :development, :test do
	gem 'rspec-rails', '~> 3.6'
	gem 'capybara'
end
```
3) Verificar em config/database.yml se existe o banco de dados de testes
```
$ rails db:create:all
```
4) Instalar o rspec-rails
```
$ rails g rspec:install
```
5) Instalar a gem commands
```ruby
group :development do
	gem 'spring-commands-rspec'
end
```
```
$ bundle exec spring binstub rspec
```
6) Inserir configurações em config/application.rb
```ruby
config.generators do |g|
	g.test_framework :rspec,
		fixtures: false,
	  	view_specs: false,
	  	helper_specs: false,
	  	routing_specs: false
end
```
