# -Chatgpt-Project-BookContextAI


# Chatgpt-Project-BookContextAI

- 1. 데이터 수집 및 전처리
    - 알라딘 데이터 수집 : 30개
    - 컬럼 : 제목, 저자, 목차, 책소개, 구매 링크, 가격, 장르, 책속에서(책 페이지 문구), 편집장의 선택
    - 장르(주제 분류) : 무조건 첫번째 줄의 세번째까지만 수집
    - 한명당 한 섹터씩 → 10년치 (2013-08-17 ~ 2023-08-27) / 너무 버겁다 : 5년치
        - 사회과학
        
    - 1) 알라딘 크롤링 이슈 (😤)
        1. beautifulsoup requests 안됨 → 동적으로 생성된 정보 로드 못함 → 일정 시간 후나 특정 사용자 액션에 의해 로드되는 콘텐츠의 경우 Selenium과 같은 도구를 사용해야 합
        2. ****selenium 사용 안됨****
            
            ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/6d6a8552-b3a8-4ae8-9f74-fc18491512af/Untitled.png)
            
            - 1, 2 방법 해봤지만 안됨
            - 결론: 웹피이지 접근 제한 인듯 (아오,,)
            
    
    - 2) 교보문교 크롤링 (성공)
        - 교보문교 사이트로 크롤링 시도
        
        1. ****selenium 라이브러리 (성공)****
        
        - 목차 크롤링 코드
            
            ```jsx
            from selenium.webdriver import Chrome, ChromeOptions
            from selenium.webdriver.common.by import By
            from selenium.webdriver.support.ui import WebDriverWait
            from selenium.webdriver.support import expected_conditions as EC
            
            op = ChromeOptions()
            op.add_argument('--headless')
            op.add_argument('--no-sandbox')
            op.add_argument('--disable-dev-shm-usage')
            
            driver = Chrome(options=op)
            
            url = "https://product.kyobobook.co.kr/detail/S000202671445"
            driver.get(url)
            
            # 웹 페이지 로딩을 기다립니다.
            wait = WebDriverWait(driver, 20)
            css_selector = "li.book_contents_item"
            data_element = wait.until(EC.presence_of_element_located((By.CSS_SELECTOR, css_selector)))
            
            print(data_element.text)
            
            # 브라우저를 종료합니다.
            driver.quit()
            ```
            
            (실행결과)
            
            ```jsx
            서문-과학 공부의 즐거움
            
            1. 그럴법한 이야기와 확실한 진리 (인문학과 과학)
            거만한 바보 ㆍ 운명적 문과의 슬픔 ㆍ 인문학과 과학의 비대칭 ㆍ 우리 집과 우리 엄마의 진실
            
            2. 나는 무엇인가 (뇌과학)
            내가 누구인지 말할 수 있는 자는 누구인가 ㆍ 1.4킬로그램의 우주 ㆍ 신경세포와 경제법칙 ㆍ 현상과 사물 자체 ㆍ 칸트 철학과 양자역학 ㆍ 측은지심과 거울신경세포 ㆍ 자유의지
            
            3. 우리는 왜 존재하는가 (생물학)
            좌파, 우파, 다윈주의 ㆍ 생명의 알파벳 ㆍ 유전자와 인생론 ㆍ 생물학 패권주의 ㆍ 사회생물학과 사회주의 ㆍ 이타 행동의 비밀
            
            4. 단순한 것으로 복잡한 것을 설명할 수 있는가 (화학)
            화학은 억울하다 ㆍ 위대한 전자 ㆍ 주기율표 ㆍ 탄소, 유능한 중도 ㆍ 환원주의 논쟁 ㆍ 통섭의 어려움
            
            5. 우리는 어디서 왔고 어디로 가는가 (물리학)
            불확정성 원리 ㆍ 상대성이론 ㆍ 별에서 온 그대 ㆍ 양자역학, 불교, 유물변증법 ㆍ 엔트로피 묵시록
            
            6. 우주의 언어인가 천재들의 놀이인가 (수학)
            수학의 아름다움 ㆍ 천재들의 지적 유희 ㆍ 난 부럽지가 않아
            
            후기-바보를 겨우 면한 자의 무모한 도전
            찾아보기
            ```
            
        
        1. beautifulsoup requests 라이브러리 (??)
        - 목차 크롤링 코드
            
            ```jsx
            from selenium.webdriver import Chrome, ChromeOptions
            from selenium.webdriver.common.by import By
            from selenium.webdriver.support.ui import WebDriverWait
            from selenium.webdriver.support import expected_conditions as EC
            
            op = ChromeOptions()
            op.add_argument('--headless')
            op.add_argument('--no-sandbox')
            op.add_argument('--disable-dev-shm-usage')
            
            driver = Chrome(options=op)
            
            url = "https://product.kyobobook.co.kr/detail/S000202671445"
            driver.get(url)
            
            # 웹 페이지 로딩을 기다립니다.
            wait = WebDriverWait(driver, 20)
            css_selector = "li.book_contents_item"
            data_element = wait.until(EC.presence_of_element_located((By.CSS_SELECTOR, css_selector)))
            
            print(data_element.text)
            
            # 브라우저를 종료합니다.
            driver.quit()
            ```
            
        - 교보문교 사이트 맞춤 컬럼 재정의:
            - 제목, 저자, 편집장의 선택, 책소개, 목차, 가격, 평점, 구매링크, 장르1, 장르2
                - 여기서 장르1, 장르2 나눈 이유는 다음과 같다.
                    
                    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/87d0a6b0-d599-446c-abff-f38f277f78fe/Untitled.png)
                    
                    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/122c00d3-870d-4ceb-8dfa-fff99abaeffe/Untitled.png)
                    
                - 두 줄 다 맞는 말이다.
                - 하지만 3개 이상은 가감히 자른다
                    
                    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/9eb2d9d7-0cab-4eae-8922-36cd15969c22/Untitled.png)
                    
        - (실행결과)
            
            정상적으로 저장 잘 되었다.. (결론: 교보문교 짱)
            
            ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/0353c4a4-95d1-4208-a841-378eaed6f690/Untitled.png)
            
            ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/7e53c0f3-04b8-475b-aff3-a894394ea932/Untitled.png)
            
    
    - 3) 교보문교 장르 재정의 및 크롤링
