# プロジェクト設定

## 基本設定
```yaml
プロジェクト名: MIRAI (Migration Immigration Resource AI)
開始日: 2026年1月16日
目標: 4ヶ月でフルローンチ（全87機能実装）
技術スタック:
  frontend:
    - Next.js 15 App Router
    - React 19
    - TypeScript 5.7+
    - Tailwind CSS 3.4+
    - Shadcn/ui (Radix UI)
  backend:
    - tRPC 11.0+
    - Prisma 6.0+
    - NextAuth.js v5
    - Redis 7.2+ (Upstash)
    - BullMQ 5.0+
  database:
    - PostgreSQL 16 (Neon推奨)
    - Redis 7.2 (Upstash)
    - Pinecone / Weaviate (Vector DB)
  ai:
    - OpenAI GPT-4 Turbo
    - Anthropic Claude 3.5 Sonnet
    - OpenAI Whisper API
```

## 開発環境
```yaml
ポート設定:
  # 複数プロジェクト並行開発のため、一般的でないポートを使用
  frontend: 3347
  backend: 8547
  database: 5434

環境変数:
  設定ファイル: .env.local（ルートディレクトリ）
  必須項目:
    - DATABASE_URL=postgresql://username:password@host:5434/mirai
    - NEXTAUTH_URL=http://localhost:3347
    - NEXTAUTH_SECRET=[ランダムな32文字以上の文字列]
    - REDIS_URL=[Upstash Redis URL]
    - OPENAI_API_KEY=[OpenAI APIキー]
    - ANTHROPIC_API_KEY=[Anthropic APIキー]
    - PINECONE_API_KEY=[Pinecone APIキー]
    - PINECONE_ENVIRONMENT=[Pinecone環境名]
```

## テスト認証情報
```yaml
開発用アカウント:
  # 外国人ユーザー
  foreign_national:
    email: test.foreign@mirai.local
    password: Test1234!@#$

  # 企業管理者
  company_admin:
    email: test.company@mirai.local
    password: Test1234!@#$

  # 登録支援機関
  support_org:
    email: test.support@mirai.local
    password: Test1234!@#$

  # 行政書士
  scrivener:
    email: test.scrivener@mirai.local
    password: Test1234!@#$

  # システム管理者
  admin:
    email: admin@mirai.local
    password: Admin1234!@#$
```

## コーディング規約

### 命名規則
```yaml
ファイル名:
  - コンポーネント: PascalCase.tsx (例: ApplicationForm.tsx)
  - ユーティリティ: camelCase.ts (例: formatVisaType.ts)
  - 定数: UPPER_SNAKE_CASE.ts (例: VISA_TYPES.ts)
  - tRPC Router: camelCase.ts (例: applicationRouter.ts)

変数・関数:
  - 変数: camelCase (例: applicantName)
  - 関数: camelCase (例: calculateApprovalScore)
  - 定数: UPPER_SNAKE_CASE (例: MAX_FILE_SIZE)
  - 型/インターフェース: PascalCase (例: ApplicationData)
  - Enum: PascalCase (例: VisaType, UserRole)
```

### コード品質
```yaml
必須ルール:
  - TypeScript: strictモード有効
  - 未使用の変数/import禁止
  - console.log本番環境禁止（開発環境のみ許可）
  - エラーハンドリング必須（try-catch、エラーバウンダリ）
  - 関数行数: 100行以下
  - ファイル行数: 700行以下
  - 複雑度: 10以下
  - 行長: 120文字

フォーマット:
  - インデント: スペース2つ
  - セミコロン: あり
  - クォート: シングル
  - トレイリングコンマ: あり（ES5）
  - Prettier設定: .prettierrc に記載
```

## プロジェクト固有ルール

### APIエンドポイント（tRPC）
```yaml
命名規則:
  - Router名: camelCase + "Router" (例: applicationRouter)
  - Procedure名: camelCase (例: createApplication, getApplicationById)
  - Mutation: create/update/delete (例: createApplication)
  - Query: get/list (例: getApplicationById, listApplications)

ディレクトリ構造:
  src/
    server/
      routers/
        application.ts
        user.ts
        document.ts
      trpc.ts
```

### 型定義
```yaml
配置:
  - 共通型: src/types/index.ts
  - Prisma型: @prisma/client から自動生成
  - tRPC型: tRPC から自動推論

同期ルール:
  - Prisma Schemaが真実の源（Single Source of Truth）
  - 型は自動生成を優先、手動定義は最小限に
```

### 多言語対応（i18n）
```yaml
対応言語: 11言語
  - ja: 日本語
  - en: 英語
  - zh-CN: 中国語簡体字
  - zh-TW: 中国語繁体字
  - vi: ベトナム語
  - id: インドネシア語
  - ne: ネパール語
  - th: タイ語
  - my: ミャンマー語
  - tl: タガログ語
  - km: クメール語

翻訳ファイル配置:
  locales/
    ja.json
    en.json
    zh-CN.json
    ...

使用方法:
  import { useTranslations } from 'next-intl';
  const t = useTranslations('namespace');
```

### データベース設計ルール
```yaml
Prisma Schema:
  - テーブル名: PascalCase単数形 (例: User, Application)
  - カラム名: camelCase (例: createdAt, visaType)
  - Relation: 明示的に定義（外部キー、onDelete、onUpdate）
  - Index: パフォーマンス重要なカラムに設定
  - Enum: データベース側で定義（型安全性）

Row Level Security (RLS):
  - 全テーブルにRLS適用（マルチテナント）
  - firmId/companyId/supportOrgIdでデータ分離
  - Prisma Middlewareで自動フィルタリング
```

### セキュリティルール
```yaml
認証・認可:
  - NextAuth.js v5でセッション管理
  - JWT + データベースセッション併用
  - RBAC: 9ロール（UserRoleEnum）
  - 二要素認証: 行政書士・管理者は必須

ファイルアップロード:
  - 許可形式: PDF、JPEG、PNG のみ
  - 最大サイズ: 10MB
  - マルウェアスキャン: 本番環境でClamAV使用
  - ストレージ: AWS S3 / Google Cloud Storage

API保護:
  - CSRF対策: Next.js標準機能
  - レート制限: 100リクエスト/分/ユーザー（Redis + tRPC middleware）
  - 入力バリデーション: Zod必須
  - SQLインジェクション対策: Prisma（パラメータ化クエリ）
```

## 🆕 最新技術情報（知識カットオフ対応）
```yaml
# 破壊的変更・重要な更新

Next.js 15:
  - App Router安定版
  - Partial Prerendering (PPR) 実験的サポート
  - Turbopack安定版（本番ビルド高速化）
  - React 19統合

tRPC 11:
  - クライアント側バッチング改善
  - エラーハンドリング強化
  - Zod統合強化

Prisma 6:
  - TypedSQL対応
  - パフォーマンス最適化
  - JSON型フィールドサポート強化

NextAuth.js v5:
  - 完全TypeScript書き換え
  - Edge Runtime対応
  - 改善された型推論
```

## プロジェクト構造
```
MIRAI/
├── src/
│   ├── app/                    # Next.js 15 App Router
│   │   ├── [locale]/          # 多言語対応
│   │   │   ├── page.tsx       # トップページ
│   │   │   ├── register/      # ユーザー登録
│   │   │   ├── login/         # ログイン
│   │   │   ├── dashboard/     # ダッシュボード
│   │   │   └── ...
│   │   ├── api/               # API Routes（tRPC統合）
│   │   └── layout.tsx
│   ├── components/            # Reactコンポーネント
│   │   ├── ui/               # Shadcn/ui コンポーネント
│   │   ├── forms/            # フォームコンポーネント
│   │   └── layouts/          # レイアウトコンポーネント
│   ├── server/                # バックエンド
│   │   ├── routers/          # tRPC Routers
│   │   ├── trpc.ts           # tRPC設定
│   │   └── auth.ts           # NextAuth.js設定
│   ├── lib/                   # ユーティリティ
│   │   ├── prisma.ts         # Prisma Client
│   │   ├── redis.ts          # Redis Client
│   │   └── ai/               # AI統合（GPT-4/Claude/Whisper）
│   ├── types/                 # 型定義
│   └── styles/                # グローバルCSS
├── prisma/
│   ├── schema.prisma          # データベーススキーマ
│   ├── migrations/            # マイグレーション
│   └── seed.ts                # シードデータ
├── locales/                   # 多言語翻訳ファイル
│   ├── ja.json
│   ├── en.json
│   └── ...
├── public/                    # 静的ファイル
├── docs/                      # ドキュメント
│   ├── requirements.md        # 要件定義書
│   └── SCOPE_PROGRESS.md      # 進捗管理表
├── .env.local                 # 環境変数（Git管理外）
├── .env.example               # 環境変数サンプル
├── .eslintrc.json             # ESLint設定
├── .prettierrc                # Prettier設定
├── tsconfig.json              # TypeScript設定
├── next.config.js             # Next.js設定
├── tailwind.config.ts         # Tailwind CSS設定
├── package.json
└── CLAUDE.md                  # このファイル
```

## AI統合ガイドライン

### OpenAI GPT-4 Turbo使用方法
```typescript
// 申請書類最適化
import OpenAI from 'openai';

const openai = new OpenAI({ apiKey: process.env.OPENAI_API_KEY });

async function optimizeApplication(applicationData: ApplicationData) {
  const response = await openai.chat.completions.create({
    model: 'gpt-4-turbo-preview',
    messages: [
      {
        role: 'system',
        content: 'あなたは在留資格申請の専門家です。申請書類を最適化してください。'
      },
      {
        role: 'user',
        content: JSON.stringify(applicationData)
      }
    ],
    temperature: 0.3, // 一貫性重視
  });

  return response.choices[0].message.content;
}
```

### Anthropic Claude 3.5使用方法
```typescript
// 理由書生成
import Anthropic from '@anthropic-ai/sdk';

const anthropic = new Anthropic({ apiKey: process.env.ANTHROPIC_API_KEY });

async function generateReasonStatement(background: string) {
  const response = await anthropic.messages.create({
    model: 'claude-3-5-sonnet-20250219',
    max_tokens: 4096,
    messages: [
      {
        role: 'user',
        content: `以下の背景情報を基に、在留資格認定証明書交付申請の理由書を作成してください。\n\n${background}`
      }
    ],
  });

  return response.content[0].text;
}
```

### Vector DB（Pinecone）使用方法
```typescript
// 類似承認事例検索
import { Pinecone } from '@pinecone-database/pinecone';

const pinecone = new Pinecone({ apiKey: process.env.PINECONE_API_KEY });
const index = pinecone.index('approved-cases');

async function findSimilarCases(applicationEmbedding: number[]) {
  const results = await index.query({
    vector: applicationEmbedding,
    topK: 5,
    includeMetadata: true,
  });

  return results.matches;
}
```

## パフォーマンス最適化

### React Server Components活用
```typescript
// サーバーコンポーネント（デフォルト）
async function ApplicationList() {
  const applications = await prisma.application.findMany(); // DBクエリ可能
  return <div>{applications.map(...)}</div>;
}

// クライアントコンポーネント（必要な場合のみ）
'use client';
function InteractiveForm() {
  const [state, setState] = useState();
  // インタラクティブな処理
}
```

### 画像最適化
```typescript
import Image from 'next/image';

// Next.js Image コンポーネント使用
<Image
  src="/residence-card.jpg"
  alt="在留カード"
  width={800}
  height={600}
  priority // LCPに重要な画像のみ
/>
```

### キャッシング戦略
```typescript
// Redis + React Query
import { useQuery } from '@tanstack/react-query';

function useApplications() {
  return useQuery({
    queryKey: ['applications'],
    queryFn: () => trpc.application.list.query(),
    staleTime: 5 * 60 * 1000, // 5分間キャッシュ
  });
}
```

## テスト戦略

### E2Eテスト（Playwright）
```yaml
テストケース:
  - ユーザー登録フロー（4ユーザータイプ）
  - ログイン・ログアウト
  - 在留資格申請作成フロー
  - AI機能（申請書最適化、理由書生成）
  - ファイルアップロード
  - 多言語切り替え
  - 管理者機能

実行コマンド:
  npm run test:e2e
```

### 単体テスト（Vitest）
```yaml
対象:
  - ユーティリティ関数
  - バリデーションロジック
  - tRPC Procedures

実行コマンド:
  npm run test
```

## デプロイ設定

### Vercel設定
```yaml
環境:
  - Preview: プルリクエストごとに自動デプロイ
  - Production: mainブランチマージ時に自動デプロイ

環境変数:
  - Vercel Dashboard で設定
  - DATABASE_URL, REDIS_URL等を設定
  - シークレットは暗号化保存
```

### データベースマイグレーション
```bash
# 開発環境
npx prisma migrate dev

# 本番環境（Vercel Build Command）
npx prisma migrate deploy
```

## トラブルシューティング

### よくある問題

**問題1: Prisma Clientが型を認識しない**
```bash
# 解決方法
npx prisma generate
```

**問題2: tRPCで型エラー**
```bash
# 解決方法: TypeScriptバージョン確認
npm list typescript
# 5.7+であることを確認
```

**問題3: 多言語が表示されない**
```bash
# 解決方法: locale JSONファイル確認
ls locales/
# 必要な言語ファイルが存在するか確認
```

---

**最終更新**: 2026年1月16日
**次回更新**: フロントエンド基盤構築開始時
