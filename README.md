## BUSCOv5 - Benchmarking sets of Universal Single-Copy Orthologs.

Основные файлы буско и их описание.

Входной файл run_BUSCO.py настраивает логгер, парсит параметры и запускает пайплайн.

Он использует:

В init.py - загрузка основных модулей в общее простнарство имен.

Exception.py - просто заглушки ошибок.

BuscoLogger.py - декоратор логгер и сам логгер. Я выключил тут запись в файл, потому что это жутко раздрожало.

После парса параметров запускается BuscoConfigManager в который передаются параметры и настраивается файл конфигов (из параметров, из парамтеров баша, дефолтный в виде окружения).

После этого создаеттся BuscoMaster, передаются ему параметры и запускется run().

На первом шаге опять запускается BuscoConfigManager. В нем создается BuscoConfigMain, который конфигурируется и валидируется. 

После этого запускается harmonize_auto_lineage_settings. Настраивается задачка на запуск и запускается. Ну и плюс обработка ошибок. В самом конце независимо от результата запускается AnalysisRunner.move_log_file(self.config).

Запуск раннер в двух режимах:

BatchRunner(self.config_manager) и SingleRunner(self.config_manager) в зависимости от флага batch_mode в конфига (пока не понятно как он ставится).
 
С конфигами они адски намудрили.

Базовые параметры:

```
DEFAULT_ARGS_VALUES = {
        "out_path": os.getcwd(),
        "cpu": 1,
        "force": False,
        "restart": False,
        "quiet": False,
        "download_path": os.path.join(os.getcwd(), "busco_downloads"),
        "datasets_version": "odb10",
        "offline": False,
        "download_base_url": "https://busco-data.ezlab.org/v5/data/",
        "auto-lineage": False,
        "auto-lineage-prok": False,
        "auto-lineage-euk": False,
        "update-data": False,
        "evalue": 1e-3,
        "limit": 3,
        "use_augustus": False,
        "long": False,
        "batch_mode": False,
        "tar": False,
}

DEPENDENCY_SECTIONS = {
    "tblastn",
    "makeblastdb",
    "prodigal",
    "sepp",
    "metaeuk",
    "augustus",
    "etraining",
    "gff2gbSmallDNA.pl",
    "new_species.pl",
    "optimize_augustus.pl",
    "hmmsearch",
}
```

Есть BaseConfig(ConfigParser) потом есть PseudoConfig(BaseConfig) потом есть BuscoConfig(ConfigParser, metaclass=ABCMeta) и еще BuscoConfigAuto(BuscoConfig) 
и еще BuscoConfigMain(BuscoConfig, BaseConfig). У кого-то классы головного мозга случились.

