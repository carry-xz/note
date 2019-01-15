### conda
 查看环境：conda info -e
 创建环境：conda create -n env_name python=2.7
 激活环境：source activate env_name
 删除环境：conda remove -n env_name --all
 安装所有包：conda install anaconda
 看某环境下安装的package：conda list -n env_name
 升级：    conda update conda
         conda update anaconda

anaconda加入环境变量  
sudo gedit ~/.bashrc  
exportPATH=~/anaconda3/bin:$PATH  
ource ~/.bashrc  
pip -V 可以查看使用的pip版本及路径  

环境复制  
     导出环境文件 conda env export --file py36.yml  这种方式不复制文件  
     在另一台计算机安装conda后 conda env create --file py36.yml  
在虚拟环境中使用  
     pip freeze >requirements.txt 输出依赖包信息到txt  
在另一个环境中使用  
     pip install -r requirements.txt  

### pyenv  
     git clone https://github.com/yyuu/pyenv.git ~/.pyenv  
     echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.bashrc  
     echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.bashrc  
     echo 'eval "$(pyenv init -)"' >> ~/.bashrc  
     exec $SHELL -l  
查看可安装的py版本  
     pyenv install --list  
安装anaconda  
     pyenv install anaconda3-5.1.0  
安装python3.5.2  
     pyenv install 3.5.2  
查看已经安装的虚拟环境  
     pyenv versions  
卸载：pyenv uninstall x.x.x  
安装或卸载后要rehash  
         pyenv rehash  
查看当前环境：pyenv version  
更新数据库  
     pyenv rehash  
全局版本切换：  
     pyenv global anaconda-2.4.0  
回到系统环境：pyenv deactivate  
安装插件pyenv-virtualenv  
     git clone https://github.com/yyuu/pyenv-virtualenv.git ~/.pyenv/plugins/pyenv-virtualenv  
     echo 'eval "$(pyenv virtualenv-init -)"' >> ~/.bash_profile  
     source ~/.bash_profile  
创建虚拟环境：  
     pyenv virtualenv 2.7.1 env271（2.7.1必须已经存在）  
删除虚拟环境: rm -rf ~/.pyenv/versions/env271  
     或者 pyenv uninstall env271  
激活：pyenv activate env271  

### 其他  
安装opencv-python后少so  
apt-get install libgtk2.0-0  

pip install opencv-contrib-python  
windows下权限问题  
pip install --user ***解决  
