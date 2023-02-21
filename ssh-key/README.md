Generating a new SSH key

// 공개키 생성
1. ssh-keygen -t ed25519 -C "your_email@example.com"

// 1번 실패 시
1.1 ssh-keygen -t rsa -b 4096 -C "your_email@example.com"


// github 공개키 등록
2. cd .ssh/id_ed25519.pub -> cat id_ed25519.pub

3. profile -> setting -> SSH and GPG keys -> new SSH key

4. title 설정 및 키 등록

5. push test