### Venv - bash:

sudo apt install python3-full python3-venv 	#	instalace venv package
python3 -m venv venv 											# vytvoří venv s názvem venv v root složce (zvolím skrz open folder)
source venv/bin/activate 										# aktivuje venv

teď mohu instalovat do venv knihovny npř : 
python -m pip install pygame pyinstaller

### Výběr interpreteru VS Code

- Stiskni Ctrl + Shift + P.
- Napiš Python: Select Interpreter.
- Vyberu interpreter jež ukazuje na mnou vytvořený venv v root dit  npř: venv/bin/python
- ověření že mám správný interpreter : 

which python  		# = output musí ukazovat do venv
pip list 						# zobrazí nainstalované packages (v spuštěném venv npř)

Doctesty - spuštění
python -m doctest _._.py -v 