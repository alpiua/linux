awk / sed
=========
**take file name and date from find**

    find . - exec ls -la | sed 's:/.*/::g' | awk '{print $9" последнее обновление: ",$6,$7 "\n"}