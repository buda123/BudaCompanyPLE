# Привязка данных Binding
## Введение в привязку данных
___
В WPF привязка (binding) является мощным инструментом программирования, без которого не обходится ни одно серьезное приложение.
Привязка подразумевает взаимодействие двух объектов: источника и приемника. Объект-приемник создает привязку к определенному свойству объекта-источника. В случае модификации объекта-источника, объект-приемник также будет модифицирован.
## Простой пример привязки
```csharp
<Window x:Class="BindingApp.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        xmlns:local="clr-namespace:BindingApp"
        mc:Ignorable="d"
        Title="MainWindow" Height="250" Width="300">
    <StackPanel>
        <TextBox x:Name="myTextBox" Height="30" />
        <TextBlock x:Name="myTextBlock" Text="{Binding ElementName=myTextBox,Path=Text}" Height="30" />
    </StackPanel>
</Window>
```
___
## Работа с привязкой в C#
Ключевым объектом при создании привязки является объект System.Windows.Data.Binding. Используя этот объект мы можем получить уже имеющуюся привязку для элемента:

```csharp
Binding binding = BindingOperations.GetBinding(myTextBlock, TextBlock.TextProperty);
```
В данном случае получаем привязку для свойства зависимостей TextProperty элемента myTextBlock.

Если в дальнейшем нам станет не нужна привязка, то мы можем воспользоваться классом BindingOperations и его методами ClearBinding()(удаляет одну привязку) 

```csharp
BindingOperations.ClearBinding(myTextBlock, TextBlock.TextProperty);
```
ClearAllBindings() (удаляет все привязки для данного элемента)

```csharp
BindingOperations.ClearAllBindings(myTextBlock);
```

___
# Свойста класса Binding
## Некоторые свойства класса Binding:

**ElementName**: имя элемента, к которому создается привязка

**IsAsync**: если установлено в True, то использует асинхронный режим получения данных из объекта. По умолчанию равно False

**Mode**: режим привязки

**Path**: ссылка на свойство объекта, к которому идет привязка

**TargetNullValue**: устанавливает значение по умолчанию, если привязанное свойство источника привязки имеет значение null

**RelativeSource**: создает привязку относительно текущего объекта

**Source**: указывает на объект-источник, если он не является элементом управления.

**XPath**: используется вместо свойства path для указания пути к xml-данным
___
# Свойста режимы привязки
## Свойство Mode объекта Binding, которое представляет режим привязки, может принимать следующие значения:
| Режим привязки| Свойства привязки|
|----------------|:---------:|
| **OneWay** | свойство объекта-приемника изменяется после модификации свойства объекта-источника |
| **OneTime** | свойство объекта-приемника устанавливается по свойству объекта-источника только один раз |
| **TwoWay** | оба объекта - применки и источник могут изменять привязанные свойства друг друга |
| **OneWayToSource** | объект-приемник, в котором объявлена привязка, меняет объект-источник |
| **Default** | по умолчанию (если меняется свойство TextBox.Text, то имеет значение TwoWay, в остальных случаях OneWay) |

## Применение режима привязки
```csharp
<StackPanel>
    <TextBox x:Name="textBox1" Height="30" />
    <TextBox x:Name="textBox2" Height="30" Text="{Binding ElementName=textBox1, Path=Text, Mode=TwoWay}" />
</StackPanel>
```
___
# Обновление привязки. UpdateSourceTrigger
Односторонняя привязка от источника к приемнику практически мгновенно изменяет свойство приемника. Но если мы используем двустороннюю привязку в случае с текстовыми полями (как в примере выше), то при изменении приемника свойство источника не изменяется мгновенно. Так, в примере выше, чтобы текстовое поле-источник изменилось, нам надо перевести фокус с текстового поля-приемника. И в данном случае в дело вступает свойство UpdateSourceTrigger класса Binding, которое задает, как будет присходить обновление. Это свойство в качестве принимает одно из значений перечисления UpdateSourceTrigger:

**PropertyChanged:** источник привязки обновляется сразу после обновления свойства в приемнике

**LostFocus:** источник привязки обновляется только после потери фокуса приемником

**Explicit:** источник не обновляется до тех пор, пока не будет вызван метод BindingExpression.UpdateSource()

**Default:** значение по умолчанию. Для большинства свойств это значение PropertyChanged. А для свойства Text элемента TextBox это значение LostFocus

В данном случае речь идет об обновлении источника привязки после изменения приемника в режимах OneWayToSource или TwoWay. То есть чтобы у нас оба текстовых поля, которые связаны режимом TwoWay, моментально обновлялись после изменения одного из них, надо использовать значение UpdateSourceTrigger.PropertyChanged:
```csharp
<StackPanel>
    <TextBox x:Name="textBox1" Height="30" />
    <TextBox x:Name="textBox2" Height="30"
        Text="{Binding ElementName=textBox1, Path=Text, Mode=TwoWay, UpdateSourceTrigger=PropertyChanged}" />
</StackPanel>
```
___
# Свойство Source
Свойство Source позволяет установить привязку даже к тем объектам, которые не являются элементами управления WPF. Например, определим класс Phone:
```csharp
class Phone
{
    public string Title { get; set; }
    public string Company { get; set; }
    public int Price { get; set; }
}
```
Теперь создадим объект этого класса и определим к нему привязку:
```csharp
<Window x:Class="BindingApp.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        xmlns:local="clr-namespace:BindingApp"
        mc:Ignorable="d"
        Title="MainWindow" Height="150" Width="300">
    <Window.Resources>
        <local:Phone x:Key="nexusPhone" Title="Nexus X5" Company="Google" Price="25000" />
    </Window.Resources>
    <Grid Background="Black">
        <Grid.RowDefinitions>
            <RowDefinition />
            <RowDefinition />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition />
            <ColumnDefinition />
        </Grid.ColumnDefinitions>
        <TextBlock Text="Модель:" Foreground="White"/>
        <TextBlock x:Name="titleTextBlock" Text="{Binding Source={StaticResource nexusPhone}, Path=Title}"
                        Foreground="White" Grid.Column="1"/>
        <TextBlock Text="Цена:" Foreground="White" Grid.Row="1"/>
        <TextBlock x:Name="priceTextBlock" Text="{Binding Source={StaticResource nexusPhone}, Path=Price}"
                        Foreground="White" Grid.Column="1" Grid.Row="1"/>
    </Grid>
</Window>
```
![11 2](https://user-images.githubusercontent.com/104549526/165707160-3097814d-8cf7-4161-9344-42430be4293a.png)
