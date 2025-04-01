<img width="1122" alt="image" src="https://github.com/user-attachments/assets/63e761c4-aece-47c2-a320-f1cc18bf916b">

<img width="920" alt="image" src="https://github.com/user-attachments/assets/55384d22-cd09-46e4-b92d-e535b7d948fd">
<img width="1115" alt="image" src="https://github.com/user-attachments/assets/9ec724e6-d46f-4849-a790-efca329d1102">
<img width="1115" alt="image" src="https://github.com/user-attachments/assets/c5c1a61b-7ff3-49fd-9dea-8104026dd1e6">
<img width="1141" alt="image" src="https://github.com/user-attachments/assets/06559a5a-ca19-40bb-bf00-d3d2cbd94ee1">





これは、次世代の [Next.js](https://nextjs.org/) を使用したSaaSスターターキットです。このキットにはランディングページ、Supabase認証（OAuth、パスワード忘れ対応など）、DrizzleORMを使用したPostgresDB、Stripeを利用した支払い処理、サブスクリプション設定、ユーザーによるサブスクリプションや支払いオプションの編集機能が含まれています。

- サインアップ/サインイン/ログアウト/パスワード忘れ/パスワードリセットの完全なフロー
- GoogleとGithubを使用したOAuth
- Stripeの料金表とStripe Checkoutを利用した顧客課金設定
- Stripe Customer Portalとの統合により、ユーザーが課金設定を管理可能
- /dashboard以下の保護されたルート
- Drizzle ORM/Postgresの統合
- Tailwind CSS/shadcnの利用
- Stripe Webhooks/APIフックを使用して顧客の現在のプランを取得

## はじめに

開発環境と本番環境の両方を設定するため、ローカル開発には`.env.local`を、本番環境には`.env`を使用してください。

### Supabaseのセットアップ
1. [Supabase](https://app.supabase.io/)で新しいプロジェクトを作成します。
2. `.env`ファイルに`SUPABASE_URL`と`SUPABASE_ANON_KEY`を追加します。
3.
![image](https://github.com/user-attachments/assets/c8eb5236-96f1-4824-9998-3c54a4bcce12)
4. OAuthフローを使用する場合、`NEXT_PUBLIC_WEBSITE_URL`を追加して、SupabaseにOAuth後のリダイレクト先を知らせます。

#### Google OAuthソーシャル認証の設定
このテンプレートを使用すれば、簡単にソーシャル認証を設定できます。まずGoogle Cloudで新しいプロジェクトを作成します。コードはすべて記述済みですので、`.env`ファイルに`GOOGLE_OAUTH_CLIENT_ID`と`GOOGLE_OAUTH_CLIENT_SECRET`を追加するだけです。

1. [こちらの手順](https://supabase.com/docs/guides/auth/social-login/auth-google?queryGroups=environment&environment=server)に従ってGoogle OAuthを設定してください。

#### Github OAuthソーシャル認証の設定
このテンプレートを使用すれば、簡単にソーシャル認証を設定できます。まずGoogle Cloudで新しいプロジェクトを作成します。コードはすべて記述済みですので、`.env`ファイルに`GITHUB_OAUTH_CLIENT_ID`と`GITHUB_OAUTH_CLIENT_SECRET`を追加するだけです。

1. [こちらの手順](https://supabase.com/docs/guides/auth/social-login/auth-github?queryGroups=environment&environment=server)に従ってGithub OAuthを設定してください。

### Postgres DBのセットアップ
このボイラープレートコードでは、任意のPostgres DBを使用できます。[Vercelのマーケットプレイス](https://vercel.com/marketplace)を利用して、Vercelプロジェクトに追加できる公式サービスを探してみてください。

`.env`ファイルに`DATABASE_URL`を追加します。例: `postgresql://${USER}:${PASSWORD}@xxxx.us-east-2.aws.neon.tech/saas-template?sslmode=require`

### ソーシャルプロバイダーを使用したOAuthの設定

#### リダイレクトURLの設定
1. Supabaseダッシュボードに移動します。
2. Authentication > Url Configurationに進みます。
3. "Site URL"に本番環境のURLを入力します。
<img width="1093" alt="image" src="https://github.com/user-attachments/assets/c10a5233-ad47-4005-b9ae-ad80fc626022">



### Stripeのセットアップ

ユーザーの支払い処理やサブスクリプション設定を行うために、[Stripe Checkout](https://stripe.com/payments/checkout)、[Stripe Pricing Tables](https://docs.stripe.com/payments/checkout/pricing-table)、[Stripe Webhooks](https://docs.stripe.com/webhooks)を使用します。

1. [Stripeに登録](https://dashboard.stripe.com/register)します。
2. `STRIPE_SECRET_KEY`を取得し、`.env`に追加します。Stripeにはテスト用と本番用のAPIキーがあります。Stripeでビジネスを認証すると、本番モードにアクセスでき、本番用APIキーが取得可能になります。それまでは[Stripeのテストモード](https://docs.stripe.com/test-mode)を使用してアプリを構築できます。

![image](https://github.com/user-attachments/assets/01da4beb-ae1d-45df-9de8-ca5e2b2c3470)

4. `stripeSetup.ts`を開き、製品情報を変更します。
5. `node --env-file=.env stripeSetup.ts`を実行してStripe製品を設定します。
6. [新しい料金表を作成](https://dashboard.stripe.com/test/pricing-tables)し、新しく作成した製品を追加します。
7. 新しい料金表を作成する際、*確認ページ*を*確認ページを表示しない*に設定します。[YOUR_PUBLIC_URL/subscribe/success](YOUR_PUBLIC_URL/subscribe/success)を値として追加します（ローカル開発では[http://localhost:3000/subscribe/success](http://localhost:3000/subscribe/success)を使用）。これにより、チェックアウト完了後にユーザーがメインダッシュボードにリダイレクトされます。本番環境では公開URLを使用します。

![image](https://github.com/user-attachments/assets/af8e9dda-3297-4e04-baa0-de7eac2a1579)


9. `.env`に`STRIPE_PUBLISHABLE_KEY`と`STRIPE_PRICING_TABLE_ID`を追加します。
![image](https://github.com/user-attachments/assets/3b1a53d3-d2d4-4523-9e0e-87b63d9108a8)

これで料金表の設定が完了しました。

### データベースのセットアップ
このボイラープレートでは、Drizzle ORMを使用してPostgresDBとやり取りします。

開始する前に、`DATABASE_URL`が設定されていることを確認してください。

必要なテーブルを作成するには、`npm drizzle-kit migrate`を実行します。

#### テーブルを変更または追加する場合
1. `/utils/db/schema.ts`に移動します。
2. テーブルを編集または追加します。
3. `npx drizzle-kit activate`を実行してマイグレーションファイルを生成します。
4. `npm drizzle-kit migrate`を実行してマイグレーションを適用します。

```bash
npm run dev
# または
yarn dev
# または
pnpm dev
# または
bun dev
```

ブラウザで[http://localhost:3000](http://localhost:3000)を開いて結果を確認してください。

## Vercelでのデプロイ

Next.jsアプリをデプロイする最も簡単な方法は、Next.jsの開発元が提供する[Vercelプラットフォーム](https://vercel.com/new?utm_medium=default-template&filter=next.js&utm_source=create-next-app&utm_campaign=create-next-app-readme)を使用することです。

詳細については、[Next.jsのデプロイメントドキュメント](https://nextjs.org/docs/deployment)を確認してください。
