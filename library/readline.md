#[6.7. readline — GNU readline interface](https://docs.python.org/3.5/library/readline.html)


通过这个模块来调用 GNU readline 接口可以实现以下要求  

1) `<TAB>` 补全
2) `↑` 或 `↓` 浏览历史命令
`...`


###补全  



    import time
    import readline


    class Command(object):
        command_list = ['time']

        @staticmethod
        def exec_command(command):
            if command in Command.command_list:
                return getattr(Command, command)()
            return 'Command Not Found'

        @staticmethod
        def time():
            return time.ctime()

    class SimpleCompleter(object):

        def __init__(self, complete_list):
            self.complete_list = complete_list

        def complete(self, text, state):
            response = None
            text = text.lower()
            if state == 0:
                if text:
                    self.matches = [ s for s in self.complete_list
                                    if s.startswith(text)]
                else:
                    self.matches = self.complete_list

            try:
                response = self.matches[state]
            except IndexError:
                pass
            return response

    readline.set_completer(SimpleCompleter(Command.command_list).complete)
    readline.parse_and_bind('tab: complete')

    if __name__ == '__main__':
        line = ''
        while line != 'stop':
            line = input('>>')
            result = Command.exec_command(line)
            print(result)

###历史命令

    import atexit
    import readline

    histfile = '/tmp/.test_history'
    try:
        readline.read_history_file(histfile)
        # default history len is -1 (infinite), which may grow unruly
        readline.set_history_length(1000)
    except FileNotFoundError:
        pass

    atexit.register(readline.write_history_file, histfile)

    if __name__ == '__main__':
        line = ''
        while line != 'exit':
            line = input('>>')

