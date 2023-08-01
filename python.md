## @classmethod

1. **工厂方法**：类方法常常被用作工厂方法，用于创建类的实例。例如，如果你有一个日期类，你可能希望用户能够直接从字符串创建一个日期实例，而不是手动输入年、月、日。你可以创建一个类方法，接收一个字符串，然后解析这个字符串，最后返回一个类的实例
```python
class Date:
    def __init__(self, year, month, day):
        self.year = year
        self.month = month
        self.day = day

    @classmethod
    def from_string(cls, date_string):
        year, month, day = map(int, date_string.split('-'))
        return cls(year, month, day)

```

2. **修改/查询类状态**：类方法还可以用于修改或查询类的状态。例如，如果你有一个类，它有一个类级别的属性，你可能希望有一个方法能够修改或查询这个属性的值。类方法可以做到这一点
```python
class MyClass:
    _class_var = 0

    @classmethod
    def increment_class_var(cls):
        cls._class_var += 1
        return cls._class_var
```
3. **继承**：由于类方法总是接收类本身作为第一个参数，因此当你调用一个类方法时，Python会将调用该方法的类传递给该方法。这意味着，如果你在一个基类中定义了一个类方法，然后在一个子类中调用它，那么传递给该方法的类将是子类，而不是基类。这使得你可以在基类中定义一些行为，但是这些行为可以被子类覆盖。在以下例子中，get_species类方法允许我们通过方法调用来获取species属性，而不是直接访问属性(这样做我们提供了更大的灵活性，例如，如果我们决定改变属性的内部表示，我们只需要修改类方法，而不需要修改访问这些属性的所有地方)
```python
class Animal:
    species = "animal"

    @classmethod
    def get_species(cls):
        return cls.species

class Dog(Animal):
    species = "dog"
    sound = "bark"

# 使用基类
print(Animal.get_species())  # 输出 "animal"

# 使用子类 Dog
print(Dog.get_species())  # 输出 "dog"
```

## 迭代器协议（Iterator Protocol）

**可迭代对象（Iterable）**：如果一个对象定义了 __iter__ 方法，并且返回一个迭代器，那么它就是一个可迭代对象。__iter__ 方法返回一个迭代器对象。Python 的内置数据类型如 list、tuple、str 等都是可迭代对象。你也可以定义自己的可迭代对象，只需在类中实现 __iter__ 方法即可。例如lit_llama/PackedDataset

**迭代器（Iterator）**：迭代器是一个更具体的概念。迭代器不仅实现了 __iter__ 方法，还实现了 __next__ 方法。通过反复调用 __next__ 方法，可以依次获取序列中的每一个元素。当序列中没有更多元素时，__next__ 方法会抛出 StopIteration 异常。例如lit_llama/PackedDatasetIterator

简单来说，可迭代对象提供了一种方式来获取一个迭代器，而迭代器则是用来进行实际的迭代操作的工具。在大部分情况下，__iter__ 方法会直接返回 self，这样的对象也被称为自迭代对象。但在某些情况下，为了支持多次迭代或者并发迭代，__iter__ 方法会返回一个新的迭代器对象。

1. 假设你有一个大的数据集，你希望同时有多个迭代器在这个数据集上进行独立的迭代。在这种情况下，每次调用 __iter__ 方法时都应该返回一个新的迭代器对象，这样每个迭代器都可以独立地维护它自己的状态（例如，当前的位置）。这样，多个迭代器就可以并发地在同一个数据集上进行迭代，而不会相互干扰。

2. 当你希望一个对象可以被多次迭代，且每次迭代都从头开始时，你也需要让 __iter__ 方法返回一个新的迭代器对象。如果 __iter__ 方法直接返回 self，那么一旦一个迭代完成，下一次迭代就无法从头开始了，因为迭代器的状态（当前的位置）已经被前一次的迭代改变了。


## 环境管理

*import*：import是Python的一个内置命令，用于加载和使用Python模块。当你在Python代码中使用import命令时，Python解释器会在指定的路径下查找并加载相应的模块。如果模块存在并且没有语法错误，那么import命令就会成功。然而，import命令并不能解决包的依赖关系或版本问题。这是pip和conda的工作。

*pip*：pip是Python的官方包管理器，用于安装和管理Python包。当你使用pip安装一个包时，pip会从Python Package Index (PyPI)下载包和其依赖，并将它们安装到你的Python环境中。pip会尝试解决包的依赖关系，并确保安装正确版本的包。然而，pip只能管理Python级别的依赖，不能管理系统级别的依赖。

*conda*：
conda会尝试解决所有的依赖关系，并确保安装正确版本的包。 conda不仅可以管理Python包，还可以管理非Python的包，甚至可以管理操作系统级别的库。
可以创建和管理独立的环境，每个环境都有自己的Python解释器和一组包。
conda并不总是能够管理所有的系统级别的依赖。有些依赖可能需要手动安装，或者使用系统的包管理器（如apt或yum）来安装
当你使用conda安装一个包时，你通常是在下载和安装已经为你的*操作系统预编译好*的二进制文件。这使得conda非常易于使用，但也限制了它的跨不同操作系统能力。

*docker&编译*：对于包含C扩展的Python包，需要进行编译步骤。编译过程中，C编译器会将C代码转换为机器代码，并链接到系统级别的库。这个过程确保了Python包可以正确地使用这些系统级别的库。如果系统级别的库没有被正确地安装，或者安装的版本不正确，编译可能会失败，或者编译出的程序可能无法正常运行。使用Docker，你可以创建一个完全定制的环境，包括操作系统、系统库、编译工具、运行时环境、应用程序和其依赖等。你可以控制每一个细节，包括如何编译你的应用程序。并允许开发者将应用和其环境打包成一个轻量级、可移植的容器，然后发布到任何支持Docker的系统上

## 离线打包python环境

### pip

##### 安装包依赖

如果一个Python项目遵循了标准的项目结构，并且包含了一个setup.py文件，那么它就可以被打包成.whl文件。以lightning为例
```shell
git clone https://github.com/Lightning-AI/lightning
cd lightning
pip wheel .
```
当你在一个Python项目的目录下运行pip wheel .命令时，pip会创建一个.whl文件，这个文件包含了项目的所有Python代码和元数据。然而，如果你的项目有任何依赖（在setup.py或pyproject.toml文件中指定），pip也会为这些依赖创建.whl文件。

这是因为pip wheel .命令的目标是创建一个可以独立安装的分发包，这个分发包应该包含项目的所有代码和依赖。当你在另一个环境中安装这个分发包时，pip会使用这些.whl文件来安装项目的依赖，而不需要从PyPI或其他源下载它们。

如果你只想为你的项目创建一个.whl文件，而不创建任何依赖的.whl文件，你可以使用python setup.py bdist_wheel命令。这个命令只会为你的项目创建一个.whl文件，不会为依赖创建.whl文件。然而，请注意，如果你在另一个环境中安装这个.whl文件，你可能需要手动安装项目的依赖。

##### 拷贝python环境

在两个Linux系统之间离线拷贝一个Python环境，可以考虑以下步骤：
1. 导出环境的依赖列表： 在源系统中，你可以使用pip freeze > requirements.txt命令来创建一个包含所有已安装包及其版本的列表。

2. 下载所有依赖的包： 在源系统中，你可以使用pip download -r requirements.txt -d /path/to/wheels命令来下载requirements.txt中列出的所有包的.whl文件。这些文件会被下载到/path/to/wheels目录中。

3. 拷贝.whl文件和requirements.txt文件： 你可以使用任何你喜欢的方法（例如USB驱动器，SCP，rsync等）来将/path/to/wheels目录和requirements.txt文件拷贝到目标系统。

4. 在目标系统中安装包： 在目标系统中，你可以使用pip install --no-index --find-links=/path/to/wheels -r requirements.txt命令来从/path/to/wheels目录中安装所有的包。

### conda

在不同的Linux系统之间离线打包环境，可以使用conda pack命令。以下是具体步骤：

1. 在源系统中，首先确定你的conda环境名称，假设为myenv。

2. 在源系统中，运行conda pack -n myenv命令。这将创建一个名为myenv.tar.gz的文件，其中包含了环境myenv的所有内容。

3. 将myenv.tar.gz文件复制到目标系统。你可以使用任何你喜欢的方法（例如USB驱动器，SCP，rsync等）。

4. 在目标系统中，解压myenv.tar.gz文件。例如，你可以运行mkdir myenv && tar -xzf myenv.tar.gz -C myenv命令。

5. 在目标系统中，激活环境。你可以运行source myenv/bin/activate命令









