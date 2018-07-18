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
##### Test Double
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

```ruby
# fase de setup
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

## Factory Girl/Bot e VCR

##### Factory Girl / Factory Bot

1) Instalação:

```ruby
group :development, :test do
  gem 'factory_bot_rails', '~> 4.0'
end
```
2) Configuração do spec/rails_helper:
```ruby
config.include FactoryBot::Syntax::Methods
```
3) Criar pasta spec/factories

4) Exemplo de factorie: spec/factories/customer.rb
```ruby
FactoryBot.define do
    factory :customer do
        name "João Neto"
        email "joao@neto.com"
    end
end
```

5) Usando a factory (build ou create)

build (Não salva) e create (salva)
```ruby
RSpec.describe Custome, type: :model do
    it 'Create a Custome' do
        customer = create(:customer)
        expect(customer.name).to eq("Joao")
    end
end
```

##### Gem Faker
1) Instalar Faker no Gemfile
```ruby
group :development, :test do
    ...
    gem 'faker'
end
```
2) Integrar com o factory
```ruby
FactoryBot.define do
    factory :customer do
        name Faker::Name.name
        email Faker::Internet.email
    end
end
```
##### Herança

Com o conceito de herança, podemos determinar os atributos esperados para uma determinada regra de negocio
```ruby
FactoryBot.define do
    factory :customer do
        name Faker::Name.name
        email Faker::Internet.email
        
        factory :customer_vip do
            vip true
            days_to_pay 30
        end

        factory :customer_default do
            vip false
            days_to_pay 15
        end
    end
end
```
```ruby
# chama a fábrica customer_vip com os valores setados de vip edays_to_pay
it 'Create a Custome' do
    customer = create(:customer_vip)
    expect(customer.vip).to eq(true)
end

# chama a fábrica customer_default com os valores setados de vip edays_to_pay
it 'Create a Custome' do
    customer = create(:customer_default)
    expect(customer.vip).to eq(false)
end
```
##### attributes_for
```ruby
# Devolve todos os atributos de customer
it 'Create a Customer' do
    attrs = attributes_for(:customer)
    puts attrs
end
```
##### Sequences
```ruby
FactoryBot.define do
    factory :customer do
        name Faker::Name.name
        
        sequence(:email) { |n| "meu_email-#{n}@email.com" }
        
        factory :customer_vip do
            vip true
            days_to_pay 30
        end

        factory :customer_default do
            vip false
            days_to_pay 15
        end
    end
end
```
##### Associações - Belongs To
```
$ rails g model Order description customer:references
```
```ruby
# O customer associa automaticamente e instancia o customer da fabrica caso ja esteja criado a mesma

FactoryBot.define do
    factory :customer do
        customer
    end
end

it 'Create a Order' do
    order = create(:order)
    expect(order.customer).to be_kind_of(Customer)
end
```
ou
```ruby
it 'Sobrescrever um customer' do
    customer = create(:customer)
    order = create(:order, customer: customer)
    expect(order.customer).to be_kind_of(Customer)
end
```
##### Create List
```ruby
it 'Create a List' do
    order = create_list(:order, 3)
    expect(orders.count).to eq(3)
end
```
##### Associações - has_many
```ruby
class Customer
    has_many :orders
class Order
    belongsto :customer

FactoryBot.define do
    factory :customer do
        name Faker::Name.name
        email Faker::Internet.email

        transient do
            qtd_orders 3
        end
        
        trait :with_orders do
            after(:create) do |customer, evaluator|
                create_list(:order, evaluator.qtd_orders, customer: customer)
            end
        end
    end
end

it 'has_many' do
    customer = create(:customer, :with_orders)
    expect(customers.orders.count).to eq(3)
end
```
##### FactoryBot Lint
verifica as validações e avisa possiveis erros de validação
```ruby
# spec_helper.rb
config.before(:suit) do
    FactoryBot.lint
end
```
##### HTTParty
```ruby
gem 'httparty'

HTTParty.get('url')
```
##### Webmock
Permite fazer chamadas fake a uma url, desabilita automaticamente o acesso ao http
```ruby
gem 'webmock'
```
```ruby
# spec_helper
require 'webmock/rspec'
```
```ruby
it 'content_type' do
    stub_request(:get, "https://jsonplaceholder.typicode.com/posts/2").
        to_return(status: 200, body: "", headers: { 'content-type': 'application/json'})

    response = HTTParty.get("https://jsonplaceholder.typicode.com/posts/3")
    content_type = response.headers['content-type']
    expect(content_type).to match(/application\/json/)
end
```
##### VCR
```ruby
gem 'vcr'
```
```ruby
VCR.configure do |config|
    config.cassette_library_dir = "spec/fixtures/vcr_cassettes"
    config.hook_into :webmock
end
```
```ruby
it 'content_type' do
    VCR.use_cassette("jsonplaceholder/posts") do
        response = HTTParty.get("https://jsonplaceholder.typicode.com/posts/3")
        content_type = response.headers['content-type']
        expect(content_type).to match(/application\/json/)
    end
end
```
##### Testando Models
```
$ rails g model Category description
$ rails g model Product description price:decimal category:references
```
# adicionar gem shoulda_matchers
```ruby
gem 'shoulda-matchers', '~> 3.1'
```
# rails_helpers
```ruby
Shoulda::Matchers.configure do |config|
    config.integrate do |with|
      with.test_framework :rspec
      with.library :rails
    end
end
```
```ruby
# app/models/product
class Product < ApplicationRecord
    belongs_to :category
  
    validates :description, :price, :category, presence: true
  
    def full_description
      "#{self.description} - #{self.price}"
    end
end
```
```ruby
# app/models/category
class Category < ApplicationRecord
end
```
// Gerar os arquivos de teste
```
$ rails g rspec:model product
$ rails g rspec:model category
```
```ruby
# spec/models/category_spec.rb
require 'rails_helper'

RSpec.describe Category, type: :model do
  pending "add some examples to (or delete) #{__FILE__}"
end
```
```ruby
# spec/models/categories.rb
FactoryBot.define do
  factory :category do
    description Faker::Commerce.department
  end
end
```
```ruby
# spec/models/products.rb
FactoryBot.define do
  factory :product do
    description Faker::Commerce.product_name
    price Faker::Commerce.price
    category
  end
end
```
```ruby
# spec/models/product_spec.rb
require 'rails_helper'

RSpec.describe Product, type: :model do
  it 'is valid with description, price and category' do
    product = create(:product)
    expect(product).to be_valid
  end

  context 'Validates' do
    it { is_expected.to validate_presence_of(:description) }
    it { is_expected.to validate_presence_of(:price) }
    it { is_expected.to validate_presence_of(:category) }
  end

  context 'Associations' do
    it { is_expected.to belong_to(:category) }
  end

  context 'Instance Methods' do
    it '#full_description' do
      product = create(:product)
      expect(product.full_description).to eq("#{product.description} - #{product.price}")
    end
  end
end
```
##### Testando controllers
```ruby
# para testar com o render_template
gem 'rails-controller_testing'
```
```
$ rails g rspec:controller customers
$ rails g devise Member
```
```ruby
# rails_helper.rb
config.include Devise::Test::ControllerHelpers, :type => :controller
config.include Warden::Test::Helpers
```
```ruby
# app/controller/customers_controller.rb
before_action :authenticate_member!, except: [:index, :show]
```
```ruby
# spec/factories/members.rb
FactoryBot.define do
    factory :member do
        email { Faker::Internet.email }
        password '123456'
        password_confirmation '123456'
    end
end
```
```ruby
# spec/controllers/customers_controller_spec.rb
RSpec.describe CustomersController, type: :controller do
    # Requisição sem o devise
    describe 'as a Guest' do
        context '#index' do
            it 'responds successfully' do
                get :index
                expect(response).to be_success
            end

            it 'responds a 200 response' do
                get :index
                expect(response).to have_http_status(200)
            end
            end

            it 'responds a 302 response (not authorized)' do
                customer = create(:customer)
                get :show, params: { id: customer.id }
                expect(response).to have_http_status(302)
            end
        end
    end

    describe 'as Logged Member' do
        before do
            @member = create(:member)
            @customer = create(:customer)
        end

        it 'Route' do
            is_expected.to route(:get, '/customers').to(action: :index)
        end

        it 'Content-Type JSON' do
            customer_params = attributes_for(:customer)
            sign_in @member
            post :create, format: :json, params: { customer: customer_params }
            expect(response.content_type).to eq('application/json')
        end

        it 'Flash Notice' do
            customer_params = attributes_for(:customer)
            sign_in @member
            post :create, params: { customer: customer_params }
            expect(flash[:notice]).to match(/successfully created/)
        end

        it 'with valid attributes' do
            customer_params = attributes_for(:customer)
            sign_in @member
            expect{
                post :create, params: { customer: customer_params }
            }.to change(Customer, :count).by(1)
        end

        it 'with invalid attributes' do
            customer_params = attributes_for(:customer, address: nil)
            sign_in @member
            expect{
                post :create, params: { customer: customer_params }
            }.not_to change(Customer, :count)
        end

        it 'responds a 200 response' do
            sign_in @member
            get :show, params: { id: @customer.id }
            expect(response).to have_http_status(200)
        end

        it 'render a :show template' do
            sign_in @member
            get :show, params: { id: @customer.id }
            expect(response).to render_template(:show)
        end
    end

end
```

##### Testando views com Capybara
```
rails g rspec:feature
```
```ruby
RSpec.feature "Customers", type: :feature do
    it 'Visit index page' do
        visit(customers_path)
        expect(page).to have_current_path(customers_path)
    end
end
```
```ruby
RSpec.feature "Customers", type: :feature do
    it 'Visit index page' do
        visit(customers_path)
        print page.html # mostra um print da página no terminal
        save_and_open_page # salva a página em memória
        page.save_screenshoot('nome') # salva um print da página
        expect(page).to have_current_path(customers_path)
    end
end
```
Configurando screenshoot
```
$ apt-get install chromium-chromedriver
```
```ruby
gem 'selenium-webdriver'
gem 'chromedriver-helper' # indispensável para o vagrant
```
```ruby
# spec/spec_helper.rb
Capybara.register_driver :chrome do |app|
    Capybara::Selenium::Driver.new app, browser: :chrome,
      options: Selenium::WebDriver::Chrome::Options.new(args: %w[headless disable-gpu])
end
Capybara.javascript_driver = :chrome
Capybara.default_max_wait_time = 5
```
```ruby
# quando o site usa js: true, ele consegue acessar o javascript da pagina
RSpec.feature "Customers", type: :feature, js: true do
    it 'Visit index page' do
        visit(customers_path)
        print page.html # mostra um print da página no terminal
        save_and_open_page # salva a página em memória
        page.save_screenshoot('nome') # salva um print da página
        expect(page).to have_current_path(customers_path)
    end
end
```
Interagindo com formulários
```ruby
# configurando spec/rails_helper.rb para trabalhar com o devise
config.include Warden::Test::Helpers

it 'Creates a customer' do
    # cria um membro e loga
    member = create(:member)
    login_as(member, :scope => :member)

    # visita a página de cadastro
    visit(new_customer_path)

    # preenche os fields do formulário
    fill_in('name', with: Faker::Name.name)
    fill_in('address', with: Faker::Address.street_adress)
    fill_in('name', with: Faker::Internet.email)

    # clica no botão
    click_button('Create Customer')

    # resultado / teste
    expect(page).to have_content('Customer was successfully created!')
end
```
xPath = linguagem de consulta para selecionar nós de um xml/html
```ruby
page.has_xpath?('.//table/tr')
```
Instalar o xPath Helper extensão para o chrome, ajuda a obter o caminho xpath

Ajax
```ruby
it 'Ajax' do
    visit(customers_path)
    click_link('Add Message')
    expect(page).to have_content(Yes!)
end
```
Find
```ruby
find_field('First Name').value
```
```
require 'rails_helper'
require_relative '../support/new_customer_form'

RSpec.feature "Customers", type: :feature, js: true do
    let(:new_customer_form) { NewCustomerForm.new }

    it 'Visit index page' do
      visit(customers_path)
      page.save_screenshot('screenshot.png')
      expect(page).to have_current_path(customers_path)
    end

    it 'Ajax' do
      visit(customers_path)
      click_link('Add Message')
      expect(page).to have_content('Yes!')
    end

    it 'Find' do
      visit(customers_path)
      click_link('Add Message')
      expect(find('#my-div').find('h1')).to have_content('Yes!')
    end

    it 'Creates a Customer - Page Object Pattern' do
      new_customer_form.login.visit_page.fill_in_with(
        name: Faker::Name.name,
        email: Faker::Internet.email,
        address: Faker::Address.street_address
      ).submit

      expect(page).to have_content('Customer was successfully created.')
    end

    it 'Creates a Customer' do
      member = create(:member)
      login_as(member, :scope => :member)

      visit(new_customer_path)

      fill_in('Name', with: Faker::Name.name)
      fill_in('Email', with: Faker::Internet.email)
      fill_in('Address', with: Faker::Address.street_address)

      click_button('Create Customer')

      expect(page).to have_content('Customer was successfully created.')
    end
end
```
```ruby
class NewCustomerForm
    include Capybara::DSL  # Capybara
    include FactoryBot::Syntax::Methods  # FactoryBot
    include Warden::Test::Helpers  # Devise
    include Rails.application.routes.url_helpers  # Routes
  
    def login
      member = create(:member)
      login_as(member, :scope => :member)
      self
    end
  
    def visit_page
      visit(new_customer_path)
      self
    end
  
    def fill_in_with(params = {})
      fill_in('Name', with: params.fetch(:name, Faker::Name.name))
      fill_in('Email', with: params.fetch(:email, Faker::Internet.email))
      fill_in('Address', with: params.fetch(:address, Faker::Address.street_address))
      self
    end
  
    def submit
      click_button('Create Customer')
    end
  end
  ```
