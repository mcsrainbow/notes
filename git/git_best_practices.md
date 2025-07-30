# Conventional Commits

## Standard

```yaml
links:
  - https://www.conventionalcommits.org
  - https://github.com/commitizen/conventional-commit-types/blob/master/index.json

conventional_commits:
  feat:     new feature
  fix:      bug fix
  docs:     documentation only changes
  style:    changes that do not affect the meaning of the code, eg: white-space, formatting, missing semi-colons, etc
  refactor: code change that neither fixes a bug nor adds a feature
  perf:     code change that improves performance
  test:     adding missing tests or correcting existing tests
  build:    changes that affect the build system or external dependencies, eg: gulp, broccoli, npm
  ci:       changes to our CI configuration files and scripts, eg: Travis, Circle, BrowserStack, SauceLabs
  chore:    other changes that do not modify src or test files
  revert:   reverts a previous commit

约定式提交:
  feat:     新功能(feature)
  fix:      修复bug
  docs:     文档(documents)
  style:    格式, 不影响代码运行
  refactor: 重构
  perf:     优化(performance), 提升性能或体验
  test:     测试
  build:    构建或依赖项更新
  ci:       持续集成(continuous integration)
  chore:    其它杂务
  revert:   撤销之前的提交

structure: <type>(scope): <subject>

alternatives:
  fix:
    - resolve
    - handle
    - correct
    - prevent
    - update
```

## Examples

- With description and breaking change footer

  ```yaml
  feat: allow provided config object to extend other configs

  BREAKING CHANGE: `extends` key in config file is now used for extending other config files
  ```

- With `!` to draw attention to breaking change

  ```yaml
  feat!: send an email to the customer when a product is shipped
  ```

- With scope and `!` to draw attention to breaking change

  ```yaml
  feat(api)!: send an email to the customer when a product is shipped
  ```

- With both `!` and BREAKING CHANGE footer

  ```yaml
  chore!: drop support for Node 6

  BREAKING CHANGE: use JavaScript features not available in Node 6.
  ```

- With no body

  ```yaml
  docs: correct spelling of CHANGELOG
  ```

- With scope

  ```yaml
  feat(lang): add Polish language
  ```

- With multi-paragraph body and multiple footers

  ```yaml
  fix: prevent racing of requests

  Introduce a request id and a reference to latest request. Dismiss
  incoming responses other than from latest request.

  Remove timeouts which were used to mitigate the racing issue but are
  obsolete now.

  Reviewed-by: Z
  Refs: #123
  ```

- With a footer that references the commit SHA that is being reverted

  ```yaml
  revert: feat(lang): add Polish language

  This reverts commit 667ecc1654a317a13331b17617d973392f415f02.
  ```

# Versioning Best Practices

## Branch Naming

```yaml
version_prefix:
  feature: ALPHA     # 功能开发分支
  develop: SNAPSHOT  # 开发主线
  test:    BETA      # 测试分支
  main:    RC        # 候选发布版 Release Candidate
  tag:     RELEASE   # 正式版
  hotfix:  HOTFIX    # 热修复
```

```yaml
分支命名实践:
  GitFlow:
    描述: 传统工作流, 适合有明确发布周期和多人协作的项目
    分支:
      feature/*: 功能开发分支, 例如 feature/login, feature/JIRA-985-login
      bugfix/*:  修复已知的非紧急缺陷, 例如 bugfix/sso-auth, bugfix/JIRA-211-sso-auth
      develop:   开发集成分支, 合并多个 feature 和 bugfix
      release/*: 候选发布分支, 例如 release/1.6.2
      hotfix/*:  生产环境热修复, 例如 hotfix/1.6.3
      main:      生产环境稳定分支
    流程:
      # 开发流程: feature/bugfix 从 develop 拉取, 完成后合并回 develop, 再通过 release 合并到 main, 并在 main 上打 tag 标记版本
      develop → feature/bugfix → develop → release → main → tag(vX.Y.Z)[推荐]
      # 紧急修复: hotfix 从 main 拉取, 完成后合并回 develop 和 main, 并在 main 上打 tag 标记版本
      main → hotfix
                ↘ develop
                ↘ main → tag(vX.Y.Z)[推荐]
    优点:
      - 明确的分支角色, 适合版本化管理
      - 方便管理发布和修复流程
    缺点:
      - 分支较多, CI/CD 流程复杂
      - 不适合高频率持续交付
  扩展(可选):
    分支:
      user/<name>/*: 个人分支, 例如 user/john/login, user/alice/sso-auth
      env/*:         环境分支, 例如 env/uat, env/prod
    说明:
      - user/<name>/* 仅用于个人实验或独立开发, 不保证会合并, 避免污染正式功能分支
      - env/* 仅用于跟踪各环境当前部署版本，不直接开发代码，更新通过合并提交

  GitHubFlow:
    描述: 简化流程, 适合持续交付和小团队
    分支:
      main:      唯一的长期分支, 始终保持可发布状态，是否发布由团队策略决定
      feature/*: 从 main 派生的功能分支
    流程:
      # 功能开发: feature 从 main 拉取, 完成后通过 Pull/Merge Request 合并回 main
      # 发布版本: 在 main 上打 tag 标记可发布版本
      main → feature → main → tag(vX.Y.Z)[推荐]
    优点:
      - 流程简单, 适合快速迭代
      - 结合 Pull/Merge Request 审核
    缺点:
      - 缺少稳定的开发集成分支
      - 版本控制需要依赖 tag

  TrunkBasedDevelopment:
    描述: 干线开发, 极简工作流, 适合持续集成/每日构建
    分支:
      main:      唯一的主干分支 (Trunk)
      feature/*: 非常短期的功能分支, 快速合并回 main
    流程:
      # 功能开发: feature 从 main 拉取, 短周期开发后快速合并回 main
      # 持续发布: 每次 main 合并都可触发构建和发布
      main → feature → main → tag(vX.Y.Z)[可选]
    说明:
      - 由于合并频繁, 通常使用 `datetime + commit_hash` 作为版本号, 例如 2025.07.30.19.06.3f9a7c1d
      - 语义化版本 (vX.Y.Z) 仅用于重要的里程碑 tag
    优点:
      - 支持高频率发布, 自动化 CI/CD 友好
      - 避免长期分支造成代码漂移(长期未合并导致代码差异累积)
    缺点:
      - 需要强大的测试和自动化保障
      - 对团队协作要求高

    GitLabFlow:
      描述: GitLab 官方推荐的工作流, 结合主干开发与环境分支, 适合多环境 CI/CD 部署
      分支:
        feature/*:  功能开发分支, 从 main 派生, 完成后通过 Merge Request 合并回 main
        main:       主分支, 用于集成和测试, 始终保持可合并状态
        staging:    预发布环境分支, 用于上线前验证
        production: 生产环境分支, 跟踪当前线上部署版本
      流程:
        # 功能开发: feature 从 main 拉取, 完成后合并回 main
        # 部署流程: main 合并到 staging 触发预发布, main 合并到 production 触发生产部署
        # 可选发布: 在 production 上打 tag 标记生产版本
        main → feature → main
                           ↘ staging
                           ↘ production → tag(vX.Y.Z)[推荐]
      说明:
        - staging/production 分支用于跟踪各环境当前部署版本
        - 更新环境分支通过合并提交完成, 不直接在这些分支上开发代码
        - main 合并到 production 时推荐打 tag 标记正式发布版本
      优点:
        - 支持多环境部署, CI/CD 友好
        - 通过环境分支管理发布, 直观跟踪当前部署状态
        - 保持 main 分支简洁, 功能通过 Merge Request 控制
      缺点:
        - 需要严格的合并策略和 CI/CD 约定
        - 对小型项目可能过于复杂
```

## Semantic Versioning

```yaml
语义化版本:
  网站: semver.org
  格式: MAJOR.MINOR.PATCH
  初始化开发版本: 0.1.0
  说明:
    MAJOR:
      描述: 主版本号
      场景: 不兼容的修改
      示例: 0.12.8 → 1.0.0
      规则: 主版本号 +1, 次版本号和修订号归零
    MINOR:
      描述: 次版本号
      场景: 向下兼容的功能性新增
      示例: 1.5.1 → 1.6.0
      规则: 次版本号 +1, 修订号归零
    PATCH:
      描述: 修订号
      场景: 向下兼容的问题修正
      示例: 1.6.1 → 1.6.2
      规则: 修订号 +1

  预发布版本:
    格式: MAJOR.MINOR.PATCH-<label>[.<identifier>]
    标签:
      alpha: 内部测试版
      beta:  公开测试版
      rc:    候选发布版  # Release Candidate
    示例:
      - 1.12.6-alpha
      - 1.12.6-beta
      - 1.12.6-rc.1
      - 1.12.6-rc.2

  元数据:
    格式: MAJOR.MINOR.PATCH[-<label>]+<metadata>
    示例:
      - 1.12.6-alpha+3f9a7c1d
      - 1.12.7+20250730

  版本演进路线示例: |
    0.1.0 → 0.1.1 → ...
      ↓
     ...
      ↓
    0.12.8
      ↓
    1.0.0
      ↓
     ...
      ↓
    1.5.1
      ↓
    1.6.0 → 1.6.1 → 1.6.2
      ↓
     ...
      ↓
    1.12.6-alpha(1.12.6-alpha+3f9a7c1d) → 1.12.6-beta → 1.12.6-rc.1 → 1.12.6-rc.2
      ↓
    1.12.7(1.12.7+20250730)
```

## Sourcecode-based Versioning

```yaml
基于源代码的版本:
  格式: <branch>-YYYYMMDDHHmm-<commit_hash>
  说明:
    branch: 分支
    YYYYMMDDHHmm: 构建日期时间
    commit_hash: 短哈希值(前8位字符)
  示例:
    - feature-login-202507281802-c4d8b21e
    - develop-202507291752-b17e5a9f
    - main-202507301906-3f9a7c1d
    - env-prod-202407312230-8d1ba5c9
  优点:
    - 直接显示构建来源分支
    - 每个版本唯一且可追溯到具体提交
    - 适合自动化持续集成
  扩展规则:
    带发布标签:
      格式: <branch>-YYYYMMDDHHmm-<commit_hash>-<label>
      标签:
        alpha: 内部测试版
        beta:  公开测试版
        rc:    候选发布版  # Release Candidate
      示例:
        - main-202507301906-3f9a7c1d-alpha
```

```yaml
# .gitlab-ci.magic-version.yml
---
variables:
  TAG_PREFIX: "v"                     # 定义 Tag 前缀, 用于去掉类似 v1.2.3 的 v

# 基于 Tag 的版本
version_tag:
  stage: magic_version
  rules:
    - if: $CI_COMMIT_TAG =~ /^.+/     # 仅在有 Tag 的情况下运行, /^.+/ 即只要有一个非空字符就返回 true
  script:
    # 取当前 Tag, 去掉 TAG_PREFIX 前缀, 例如 v1.2.3 -> 1.2.3
    - export MAGIC_VERSION=${CI_COMMIT_TAG#*"$TAG_PREFIX"}
    # 把版本号写入 build.env, 供后续 Job 使用
    - echo "MAGIC_VERSION=$MAGIC_VERSION" >> build.env
  artifacts:
    reports:
      dotenv: build.env               # 把 build.env 导出为 dotenv, 供后续 Job 使用 $MAGIC_VERSION 变量

# 基于 分支 的版本
version_branch:
  stage: magic_version
  rules:
    - if: $CI_COMMIT_BRANCH           # 当基于 分支 构建时运行
  script:
    # 生成时间戳, 格式为 YYYYMMDDHHmm, 例如 202507301906
    - VERSION_DATETIME=$(date +'%Y%m%d%H%M')
    # 拼接版本号：<branch>-YYYYMMDDHHmm-<commit_hash>
    # 例如：main-202507301906-3f9a7c1d
    # 变量说明：
    #   $CI_COMMIT_REF_SLUG  -> 分支名 slug, 例如 feature-login / main / env-prod
    #   $CI_COMMIT_SHORT_SHA -> 当前提交的短哈希值(前8位字符)
    - export MAGIC_VERSION=${CI_COMMIT_REF_SLUG}-${VERSION_DATETIME}-${CI_COMMIT_SHORT_SHA}
    # 把版本号写入 build.env, 供后续 Job 使用
    - echo "MAGIC_VERSION=$MAGIC_VERSION" >> build.env
  artifacts:
    reports:
      dotenv: build.env               # 把 build.env 导出为 dotenv, 供后续 Job 使用 $MAGIC_VERSION 变量
```

