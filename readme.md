
windows系统下vscode虚拟环境创建方式（与macos不同）
1. 创建虚拟环境  
    py -3.13-m venv .venv
2. 以管理员身份运行 PowerShell   
    Set-ExecutionPolicy RemoteSigned -Scope CurrentUser
3. 激活环境  
    .\.venv\Scripts\Activate.ps1