
## DSBot

DSBot is a chatbot framework to automate data science and machine learning processes. The framework provide several common base commands and the capability to adapt to other scenarios by creating new commands. 

## Installing

Install and update using [pip](https://pypi.org/project/pip/):

* `pip install -U dsbot`


## A Simple Example

```python
from dsbot.DSBot import DSBot

if __name__ == '__main__':

    bot_options = {
        'save_path': "data/",
    }

    dsbot = DSBot(**bot_options)
    dsbot.start()
```

Note: The system will create the **data/** directory if not exist.

## Creating New Commands

New commands extends the base class **CommandWithArgs**

```python
from dsbot.commands.base.command_with_args import CommandWithArgs

class ColumnNameCommand(CommandWithArgs):
    ...
```

Next, we add some metadata to tell the framework the intention(**tag**) and the triggers(**patterns**) of the command.

```python
from dsbot.commands.base.command_with_args import CommandWithArgs

class ColumnNameCommand(CommandWithArgs):

    tag = "show_column_name"
    patterns = [
        "Mostre os nomes das colunas",
        "Quais sao as colunas"
    ]
```

Our command **ColumnNameCommand** must know from which dataset it has to list the columns.
So, we need to add a parameter(**dataset_name**). The method **create_dataset** receives the dataset name and a trigger phrase.
Also, we have to prepare the bot answers(**responses**). 
To do this, we setup the **__init__** method.


```python
from dsbot.commands.base.command_with_args import CommandWithArgs

class ColumnNameCommand(CommandWithArgs):

    tag = "show_column_name"
    patterns = [
        "Mostre os nomes das colunas",
        "Quais sao as colunas"
    ]

    def __init__(self, parent, task_manager):
        super(ColumnNameCommand, self).__init__(parent, task_manager)
        self.responses = ["Posso mostrar as colunas do dataset"]
        self.create_argument("dataset_name", "dataset")
```
The *create_argument* function will create a property *dataset_name* to save the dataset parameter. In fact, the argument extraction will use regular expressions to select what the user types after the word *dataset*. For instance, when the user types "vamos carregar o dataset iris". The system will understand the dataset_name values as "iris". Just the word after the "dataset" trigger.
At this point, we have a base command with a dataset argument, but with the default functionality. To override the default behavior we override the **run**  method.

```python
from dsbot.commands.base.command_with_args import CommandWithArgs
from dsbot.commands.base.argument import Argument

class ColumnNameCommand(CommandWithArgs):

    tag = "show_column_name"
    patterns = [
        "Mostre os nomes das colunas",
        "Quais sao as colunas"
    ]

    def __init__(self, parent, task_manager):
        super(ColumnNameCommand, self).__init__(parent, task_manager)
        self.responses = ["Posso mostrar as colunas do dataset"]
        self.create_argument("dataset_name", "dataset")

    def run(self, context):
        dataset_name = self.dataset_name.value

        try:
            df_columns = context[dataset_name].columns
            print(df_columns)
        except Exception as e:
            print(e)
```

With the line `dataset_name = self.dataset_name.value` we retrieve the dataset_name parameter value.

Finally, we need to generate the code of this command. So we need to add all the line codes that this command will generate to the **code_generator** obejct received as a parameter of the **generate_code** method.
```python
from dsbot.commands.base.command_with_args import CommandWithArgs
from dsbot.commands.base.argument import Argument

class ColumnNameCommand(CommandWithArgs):

    tag = "show_column_name"
    patterns = [
        "Mostre os nomes das colunas",
        "Quais sao as colunas"
    ]

    def __init__(self, parent, task_manager):
        super(ColumnNameCommand, self).__init__(parent, task_manager)
        self.responses = ["Posso mostrar as colunas do dataset"]
        self.create_argument("dataset_name", "dataset")

    def run(self, context):
        dataset_name = self.dataset_name.value

        try:
            df_columns = context[dataset_name].columns
            print(df_columns)
        except Exception as e:
            print(e)

    def generate_code(self, code_generator, context):
        code_generator.write("")
        code_generator.write("# Select column from dataset")

        dataset_name = self.dataset_name.value

        code_generator.write("print({0}.columns)".format(dataset_name))
```
At this point, we have a new command ready to be used by the framework. However, to execute the bot with the new command, we need to pass it in the initial configuration options as you can see in the following example.

```python
from dsbot.DSBot import DSBot
from extensions.ColumnNameCommand import ColumnNameCommand

if __name__ == '__main__':

    bot_options = {
        'save_path': "data/",
        'force_training': True,

        'extensions': [
            ColumnNameCommand
        ],
    }

    dsbot = DSBot(**bot_options)
    dsbot.start()
```

Congrats, You have created your first command using DSBot. For a list of available commands refer to : [Commands](http://)

## The *force_training* parameter

The system requires several files such as the corpus, the bot (tensorflow files), among others. When we add new commands or extend the base commands with new ones, we must recreate those files. otherwise the system will load old files without the new commands.
To summarize, the main.py needs to have the same number of commands as the files in the data folder. For example, if we add the *ColumnNameCommand* in the extend parameter without the *force_training* parameter set to true, the system will load old serialized files and return an error. The loaded commands does not match the extended commands.
To force the training and serialization of new files, we can use the *force_training* parameter set to true.