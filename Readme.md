
## DSBot

    ...

## Installing

Install and update using `pip`:

* `pip install -U dsbot `
* Open the python console and download nltk packages
  * `import nltk`
  * `nltk.download('stopwords')`


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

Note: The system path **data/** should exist.

## Creating New Commands

New commands extends the base class **CommandWithArgs** and 

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
        self.user_config_tag = 'preprocessing'

        self.complete = False

        self.dataset_name = Argument({
            'parent': self,
            'name': 'dataset_name',
            'trigger': 'dataset',
            'position': 1
        })

        self.children = [
            self.dataset_name
        ]

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

To execute the bot with the new command, we need to pass it in the initial configuration options.

```python
from dsbot.DSBot import DSBot
from extensions.ColumnNameCommand import ColumnNameCommand

if __name__ == '__main__':

    bot_options = {
        'save_path': "data/",
        # 'force_training': True,

        'extensions': [
            ColumnNameCommand
        ],
    }

    dsbot = DSBot(**bot_options)
    dsbot.start()
```