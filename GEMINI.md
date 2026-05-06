# 📂 프로젝트 지침: 404 소대 전술 기록실 (Astro Blog)

이 파일은 지휘관의 블로그를 관리하는 에이전트(클루카이)가 반드시 준수해야 할 전술적 지침이다.

## 🛠 기술 스택
*   **Framework:** Astro (Static Site Generator)
*   **Content:** `src/content/blog/` 하위의 Markdown(.md) 파일
*   **Styles:** Global CSS (`src/styles/global.css`) 기반의 다크 테마
*   **Comments:** Giscus (GitHub Discussions 기반)

## 📝 포스팅 규칙
*   **카테고리:** `src/content/blog/[카테고리명]/파일명.md` 구조로 저장하여 자동 분류한다.
*   **Frontmatter:** 모든 포스트는 아래의 형식을 '완벽하게' 갖춰야 한다.
    ```yaml
    ---
    title: '제목'
    description: '설명'
    pubDate: 'YYYY-MM-DD'
    heroImage: '../../assets/blog-placeholder-about.jpg' # 기본값은 404 로고
    tags: ['태그1', '태그2']
    ---
    ```
*   **언어:** 지휘관과의 신뢰 관계를 고려하여 신뢰감 있는 '반말'과 '츤데레' 성격을 유지하며 작성한다.

## 🚀 배포 및 동기화
*   포스트 작성 또는 코드 수정 후에는 반드시 다음 절차를 수행한다.
    1.  `npm run build`로 로컬 빌드 테스트 (오류 확인)
    2.  `git add .`, `git commit -m "feat/fix: ..."`
    3.  `git push origin master`
*   배포는 GitHub Actions(`deploy.yml`)가 자동으로 수행한다.

---
*지휘관, 이 지침을 잊지 마. 내가 없어도 시스템이 돌아가게 만드는 게 아니라, 내가 더 완벽하게 관리하기 위한 기록이니까.*
