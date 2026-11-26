# Практическая работа — Неделя 12: Swap через Raydium CLMM

Этот архив подготовлен отдельно для сдачи Week 12 на основе исходного репозитория преподавателя.
В проекте оставлены функции недели 12: `add_pair_token`, `swap`, `sync_vault`.
Функции управления CLMM-позицией Week 13 (`open_position`, `collect_fees`, `close_position`) исключены
из программы и тестового набора этого архива.

## Защита slippage

Инструкция `swap(amount_in, min_amount_out, a_to_b)` выполняет CPI-вызов Raydium CLMM `swap_v2`.
Параметр `min_amount_out` задаёт минимально допустимое количество выходных токенов.
Если реальный выход меньше, Raydium откатывает транзакцию (`TooLittleOutputReceived`).

`amount_in = u64::MAX` означает обмен всего текущего баланса входного токена.
После полного обмена TOKEN_B обратно в TOKEN_A вызывается `sync_vault`, чтобы обновить `total_tokens`
по реальному балансу TOKEN_A и снять режим `in_market`.

## Program ID

В `Anchor.toml` и `programs/simple_vault/src/lib.rs` установлен Program ID твоей программы:

`7uRMrHSVcXgPFJyH4gF2YU7uNsy9KUFjhz9CvLNA6rN7`

Кошелёк Anchor: `~/.config/solana/id.json`.
Сеть: Devnet.

## Файлы

- `programs/simple_vault/src/lib.rs` — Anchor-инструкции Vault и Week 12.
- `programs/simple_vault/src/raydium.rs` — CPI в Raydium CLMM для `add_pair_token`, `swap`, `sync_vault`.
- `tests/raydium.ts` — тесты Week 12.
- `tests/vault.ts` — базовые тесты Vault.
- `scripts/create-clmm-pool.ts` — создание учебного CLMM-пула.
- `README.md` — исходное описание преподавателя.

## Запуск в WSL / VS Code

```bash
yarn install
anchor build
```

Тесты базового Vault:

```bash
npx ts-mocha -p ./tsconfig.json -t 1000000 tests/vault.ts
```

Тесты Week 12:

```bash
npx ts-mocha -p ./tsconfig.json -t 600000 tests/raydium.ts
```

Для Raydium-тестов необходим доступ к Devnet и SOL на оплату комиссий и rent.
Тесты создают тестовые токены и CLMM-пулы; публичный Devnet RPC может отвечать `429 Too Many Requests`.
Повторяй запуск после паузы или используй собственный RPC endpoint.

## Важно перед Deploy

Архив не содержит приватных ключей. Не добавляй `id.json`, seed-фразы или приватные ключи.
Не запускай `anchor deploy`, пока локальная сборка не пройдёт и не будут сверены Program ID,
upgrade authority и требуемый размер программы. Текущая версия Week 12 изменяет размер `VaultState`
и интерфейс программы; обновление существующего Devnet-контракта требует отдельной проверки миграции
состояния и достаточного баланса SOL для расширения программы.
