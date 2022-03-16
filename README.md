# SolanaSQL
SQL Query repository for Solana Network(currently for use on Dune Analytics @ https://dune.xyz/)

@danning.sui // Solana Cumulative Wallets (fee payers)
with user_first_ts as
(
    select    
        account_keys[0] as wallet
        , min(date_trunc('day', block_time)) as first_seen
    from solana.transactions
    where block_time is not null
    group by 1
) select first_seen as day,
            sum(count(distinct wallet)) over (order by first_seen) as cumulative_wallets
        from user_first_ts
        group by 1

// Solana MAU, last 6m
select    
    date_trunc('month', block_time) as month,
    count(distinct account_keys[0]) as mau
from solana.transactions
where block_time >= date_trunc('month', now() - interval '6 months')
        and block_time < date_trunc('month', now())
group by 1

// Solana DAU, last 30d
select    
    date_trunc('day', block_time) as day,
    count(distinct account_keys[0]) as dau
from solana.transactions
where block_time >= date_trunc('day', now() - interval '30 days')
        and block_time < date_trunc('month', now())
group by 1

// Solana DeFi DAU, last 30d
select    
    date_trunc('day', block_time) as day,
    count(distinct account_keys[0]) as dau
from solana.transactions
where (
        -- agg
        array_contains(account_keys, 'JUP6i4ozu5ydDCnLiMogSckDPpbtr7BJ4FtzYWkb5Rk') -- Jupiter Aggregator v1
        or array_contains(account_keys, 'JUP2jxvXaqu7NQY1GmNF4m1vodw12LVXYxbFL2uJvfo') -- Jupiter Aggregator v2
        -- lending
        or array_contains(account_keys, 'So1endDq2YkqhipRh3WViPa8hdiSpxWy6z3Z6tMCpAo') -- Solend Protocol
        or array_contains(account_keys, 'Port7uDYB3wk6GJAw4KT1WpTeMtSu9bTcChBHkX2LfR') -- Port Finance v1.0
        -- perpetual trading
        or array_contains(account_keys, 'mv3ekLzLbnVPNxjSKvqBpU3ZeZXPQdEC3bp5MDEBG68') -- Mango Markets V3
        or array_contains(account_keys, '5fNfvyp5czQVX77yoACa3JJVEhdRaWjPuazuWgjhTqEH') -- Mango Markets V2
        or array_contains(account_keys, 'JD3bq9hGdy38PuWQ4h2YJpELmHVGPPfFSuFkpzAd9zfu') -- Mango Markets V1
   
        -- DEX
        or array_contains(account_keys, '9xQeWvG816bUx9EPjHmaT23yvVM2ZWbrrpZb9PusVFin') -- Serum DEX V3
        or array_contains(account_keys, 'EUqojwWA2rd19FZrzeBncJsm38Jm1hEhE3zsmX3bRc2o') -- Serum DEX V2
        or array_contains(account_keys, 'BJ3jrUzddfuSrZHXSCxMUUQsjKEyLmuuyZebkcaFp2fg') -- Serum DEX V1
        or array_contains(account_keys, '4ckmDgGdxQoPDLUkDT3vHgSAkzA3QRdNq5ywwY4sUSJn') -- Serum DEX V1
        or array_contains(account_keys, '22Y43yTVxuUkoRKdm9thyRhQ3SdgQS7c7kB6UNCiaczD') -- Serum Swap
   
        or array_contains(account_keys, 'AMM55ShdkoGRB5jVYPjWziwk8m5MpwyDgsMWHaMSQWH6') -- Aldrin AMM
        or array_contains(account_keys, 'CURVGoZn8zycx6FXwwevgBTB2gVvdbGTEpvMJDbgs2t4') -- Aldrin AMM V2
       

        or array_contains(account_keys, 'RVKd61ztZW9GUwhRbbLoYVRE5Xf1B2tVscKqwZqXgEr') -- Raydium Liquidity Pool V2
        or array_contains(account_keys, '27haf8L6oxUeXrHrgEgsexjSY5hbVUWEmvv9Nyxg8vQv') -- Raydium Liquidity Pool V3
        or array_contains(account_keys, '675kPX9MHTjS2zt1qfr1NYHuzeLXfQM9H24wFSUt1Mp8') -- Raydium Liquidity Pool V4
        or array_contains(account_keys, 'EhhTKczWMGQt46ynNeRX1WfeagwwJd7ufHvCDjRxjo5Q') -- Raydium Stake
        or array_contains(account_keys, 'CBuCnLe26faBpcBP2fktp4rp8abpcAnTWft6ZrP5Q4T') -- Raydium Stake V4
        or array_contains(account_keys, '9KEPoZmtHUrBbhWN1v1KWLMkkvwY6WLtAVUCPRtRjP4z') -- Raydium Stake V5

        or array_contains(account_keys, 'SSwpkEEcbUqx4vtoEByFjSkhKdCT862DNVb52nZg1UZ') -- Saber Stable Swap
        or array_contains(account_keys, 'MERLuDFBMmsHnsBPZw2sDQZHvXFMwp8EdjudcU2HKky') -- Mercurial Stable Swap
       
        or array_contains(account_keys, '82yxjeMsvaURa4MbZZ7WZZHfobirZYkH1zF8fmeGtyaQ') -- Orca Aquafarm
        or array_contains(account_keys, 'DjVE6JNiYqPL2QXyCUUh8rNjHrbz9hXHNYt99MQ59qw1') -- Orca Token Swap
        or array_contains(account_keys, '9W959DqEETiGZocYWCQPaJ6sBmUzgfxXfqGeTEdp3aQP') -- Orca Token Swap V2
       
        or array_contains(account_keys, 'SSwpMgqNDsyV7mAgN9ady4bDVu5ySjmmXejXvy2vLt1') -- Step Finance Swap Program
       
        or array_contains(account_keys, 'SwaPpA9LAaLfeLi3a68M4DjnLqgtticKg6CnyNwgAC8') -- Swap Program ??
        )
        and block_time >= date_trunc('day', now() - interval '30 days')
        and block_time < date_trunc('day', now())
group by 1

// Solana DEX DAU, last 30d

select    
    date_trunc('day', block_time) as day,
    case when (array_contains(account_keys, 'mv3ekLzLbnVPNxjSKvqBpU3ZeZXPQdEC3bp5MDEBG68')
                or array_contains(account_keys, '5fNfvyp5czQVX77yoACa3JJVEhdRaWjPuazuWgjhTqEH')
                or array_contains(account_keys, 'JD3bq9hGdy38PuWQ4h2YJpELmHVGPPfFSuFkpzAd9zfu')) then 'Mango Markets'
        when (array_contains(account_keys, '9xQeWvG816bUx9EPjHmaT23yvVM2ZWbrrpZb9PusVFin')
                or array_contains(account_keys, '22Y43yTVxuUkoRKdm9thyRhQ3SdgQS7c7kB6UNCiaczD')
                or array_contains(account_keys, 'EUqojwWA2rd19FZrzeBncJsm38Jm1hEhE3zsmX3bRc2o')
                or array_contains(account_keys, 'BJ3jrUzddfuSrZHXSCxMUUQsjKEyLmuuyZebkcaFp2fg')
                or array_contains(account_keys, '4ckmDgGdxQoPDLUkDT3vHgSAkzA3QRdNq5ywwY4sUSJn')) then 'Serum'
        when (array_contains(account_keys, 'AMM55ShdkoGRB5jVYPjWziwk8m5MpwyDgsMWHaMSQWH6')
                or array_contains(account_keys, 'CURVGoZn8zycx6FXwwevgBTB2gVvdbGTEpvMJDbgs2t4')) then 'Aldrin'
        when (array_contains(account_keys, 'RVKd61ztZW9GUwhRbbLoYVRE5Xf1B2tVscKqwZqXgEr')
                or array_contains(account_keys, '27haf8L6oxUeXrHrgEgsexjSY5hbVUWEmvv9Nyxg8vQv')
                or array_contains(account_keys, '675kPX9MHTjS2zt1qfr1NYHuzeLXfQM9H24wFSUt1Mp8')
                or array_contains(account_keys, 'EhhTKczWMGQt46ynNeRX1WfeagwwJd7ufHvCDjRxjo5Q')
                or array_contains(account_keys, 'CBuCnLe26faBpcBP2fktp4rp8abpcAnTWft6ZrP5Q4T')
                or array_contains(account_keys, '9KEPoZmtHUrBbhWN1v1KWLMkkvwY6WLtAVUCPRtRjP4z')) then 'Raydium'
        when array_contains(account_keys, 'SSwpkEEcbUqx4vtoEByFjSkhKdCT862DNVb52nZg1UZ') then 'Saber Stable Swap'
        when array_contains(account_keys, 'MERLuDFBMmsHnsBPZw2sDQZHvXFMwp8EdjudcU2HKky') then 'Mercurial Stable Swap'
        when (array_contains(account_keys, '82yxjeMsvaURa4MbZZ7WZZHfobirZYkH1zF8fmeGtyaQ')
                or array_contains(account_keys, 'DjVE6JNiYqPL2QXyCUUh8rNjHrbz9hXHNYt99MQ59qw1')
                or array_contains(account_keys, '9W959DqEETiGZocYWCQPaJ6sBmUzgfxXfqGeTEdp3aQP')) then 'Orca'
        when array_contains(account_keys, 'SSwpMgqNDsyV7mAgN9ady4bDVu5ySjmmXejXvy2vLt1') then 'Step Finance Swap'
        when array_contains(account_keys, 'SwaPpA9LAaLfeLi3a68M4DjnLqgtticKg6CnyNwgAC8') then 'Swap Program'
        else null end as DEX,
    count(distinct account_keys[0]) as dau
from solana.transactions
where (

        -- perpetual trading
        array_contains(account_keys, 'mv3ekLzLbnVPNxjSKvqBpU3ZeZXPQdEC3bp5MDEBG68') -- Mango Markets V3
        or array_contains(account_keys, '5fNfvyp5czQVX77yoACa3JJVEhdRaWjPuazuWgjhTqEH') -- Mango Markets V2
        or array_contains(account_keys, 'JD3bq9hGdy38PuWQ4h2YJpELmHVGPPfFSuFkpzAd9zfu') -- Mango Markets V1
   
        -- DEX
        or array_contains(account_keys, '9xQeWvG816bUx9EPjHmaT23yvVM2ZWbrrpZb9PusVFin') -- Serum DEX V3
        or array_contains(account_keys, 'EUqojwWA2rd19FZrzeBncJsm38Jm1hEhE3zsmX3bRc2o') -- Serum DEX V2
        or array_contains(account_keys, 'BJ3jrUzddfuSrZHXSCxMUUQsjKEyLmuuyZebkcaFp2fg') -- Serum DEX V1
        or array_contains(account_keys, '4ckmDgGdxQoPDLUkDT3vHgSAkzA3QRdNq5ywwY4sUSJn') -- Serum DEX V1
        or array_contains(account_keys, '22Y43yTVxuUkoRKdm9thyRhQ3SdgQS7c7kB6UNCiaczD') -- Serum Swap
   
        or array_contains(account_keys, 'AMM55ShdkoGRB5jVYPjWziwk8m5MpwyDgsMWHaMSQWH6') -- Aldrin AMM
        or array_contains(account_keys, 'CURVGoZn8zycx6FXwwevgBTB2gVvdbGTEpvMJDbgs2t4') -- Aldrin AMM V2
       

        or array_contains(account_keys, 'RVKd61ztZW9GUwhRbbLoYVRE5Xf1B2tVscKqwZqXgEr') -- Raydium Liquidity Pool V2
        or array_contains(account_keys, '27haf8L6oxUeXrHrgEgsexjSY5hbVUWEmvv9Nyxg8vQv') -- Raydium Liquidity Pool V3
        or array_contains(account_keys, '675kPX9MHTjS2zt1qfr1NYHuzeLXfQM9H24wFSUt1Mp8') -- Raydium Liquidity Pool V4
        or array_contains(account_keys, 'EhhTKczWMGQt46ynNeRX1WfeagwwJd7ufHvCDjRxjo5Q') -- Raydium Stake
        or array_contains(account_keys, 'CBuCnLe26faBpcBP2fktp4rp8abpcAnTWft6ZrP5Q4T') -- Raydium Stake V4
        or array_contains(account_keys, '9KEPoZmtHUrBbhWN1v1KWLMkkvwY6WLtAVUCPRtRjP4z') -- Raydium Stake V5

        or array_contains(account_keys, 'SSwpkEEcbUqx4vtoEByFjSkhKdCT862DNVb52nZg1UZ') -- Saber Stable Swap
        or array_contains(account_keys, 'MERLuDFBMmsHnsBPZw2sDQZHvXFMwp8EdjudcU2HKky') -- Mercurial Stable Swap
       
        or array_contains(account_keys, '82yxjeMsvaURa4MbZZ7WZZHfobirZYkH1zF8fmeGtyaQ') -- Orca Aquafarm
        or array_contains(account_keys, 'DjVE6JNiYqPL2QXyCUUh8rNjHrbz9hXHNYt99MQ59qw1') -- Orca Token Swap
        or array_contains(account_keys, '9W959DqEETiGZocYWCQPaJ6sBmUzgfxXfqGeTEdp3aQP') -- Orca Token Swap V2
       
        or array_contains(account_keys, 'SSwpMgqNDsyV7mAgN9ady4bDVu5ySjmmXejXvy2vLt1') -- Step Finance Swap Program
       
        or array_contains(account_keys, 'SwaPpA9LAaLfeLi3a68M4DjnLqgtticKg6CnyNwgAC8') -- Swap Program ??


        )
        and block_time >= date_trunc('day', now() - interval '30 days')
        and block_time < date_trunc('day', now())
group by 1,2

//  Solana NFT Marketplace DAU, last 30d

select    
    date_trunc('day', block_time) as day,
    case when (array_contains(account_keys, 'MEisE1HzehtrDpAAT8PnLHjpSSkRYakotTuJRPjTpo8')
                or array_contains(account_keys, 'M2mx93ekt1fmXSVkTrUL9xVFHkmME8HTUi5Cyc5aF7K'))  then 'Magic Eden'
        when (array_contains(account_keys, 'cndyAnrLdpjq1Ssp1z8xxDsB8dxe7u4HL5Nxi2K5WXZ')
                or array_contains(account_keys, 'cndy3Z4yapfJBmL3ShUp5exZKqR3z33thTzeNMm2gRZ')) then 'Metaplex NFT Candy Machine'
        when array_contains(account_keys, 'CJsLwbP1iu5DuUikHEJnLfANgKy6stB2uFgvBBHoyxwz') then 'Solanart'
        when (array_contains(account_keys, 'A7p8451ktDCHq5yYaHczeLMYsjRsAkzc3hCXcSrwYHU7')
                or array_contains(account_keys, '7t8zVJtPCFAqog1DcnB6Ku1AVKtWfHkCiPi1cAvcJyVF')) then 'DigitalEyes'
        when array_contains(account_keys, '617jbWo616ggkDxvW1Le8pV38XLbVSyWY8ae6QUmGBAU') then 'Solsea'
        else null end as nft_project,
    count(distinct account_keys[0]) as dau
from solana.transactions
where (
        array_contains(account_keys, 'MEisE1HzehtrDpAAT8PnLHjpSSkRYakotTuJRPjTpo8') -- Magic Eden NFT Marketplace
        or array_contains(account_keys, 'M2mx93ekt1fmXSVkTrUL9xVFHkmME8HTUi5Cyc5aF7K') -- Magic Eden NFT Marketplace v2
       
        or array_contains(account_keys, 'cndyAnrLdpjq1Ssp1z8xxDsB8dxe7u4HL5Nxi2K5WXZ') -- Metaplex NFT Candy Machine v1
        or array_contains(account_keys, 'cndy3Z4yapfJBmL3ShUp5exZKqR3z33thTzeNMm2gRZ') -- Metaplex NFT Candy Machine v2
       
        or array_contains(account_keys, 'CJsLwbP1iu5DuUikHEJnLfANgKy6stB2uFgvBBHoyxwz') -- Solanart NFT Marketplace
       
        or array_contains(account_keys, 'A7p8451ktDCHq5yYaHczeLMYsjRsAkzc3hCXcSrwYHU7') -- DigitalEyes NFT Marketplace
        or array_contains(account_keys, '7t8zVJtPCFAqog1DcnB6Ku1AVKtWfHkCiPi1cAvcJyVF') -- DigitalEyes Direct Sell
       
        or array_contains(account_keys, '617jbWo616ggkDxvW1Le8pV38XLbVSyWY8ae6QUmGBAU') -- Solsea NFT Marketplace
        )
        and block_time >= date_trunc('day', now() - interval '30 days')
        and block_time < date_trunc('day', now())
group by 1, 2

// Solana DEX MAU, last 6m
select    
    date_trunc('month', block_time) as month,
    count(distinct account_keys[0]) as mau
from solana.transactions
where (

        -- perpetual trading
        array_contains(account_keys, 'mv3ekLzLbnVPNxjSKvqBpU3ZeZXPQdEC3bp5MDEBG68') -- Mango Markets V3
        or array_contains(account_keys, '5fNfvyp5czQVX77yoACa3JJVEhdRaWjPuazuWgjhTqEH') -- Mango Markets V2
        or array_contains(account_keys, 'JD3bq9hGdy38PuWQ4h2YJpELmHVGPPfFSuFkpzAd9zfu') -- Mango Markets V1
   
        -- DEX
        or array_contains(account_keys, '9xQeWvG816bUx9EPjHmaT23yvVM2ZWbrrpZb9PusVFin') -- Serum DEX V3
        or array_contains(account_keys, 'EUqojwWA2rd19FZrzeBncJsm38Jm1hEhE3zsmX3bRc2o') -- Serum DEX V2
        or array_contains(account_keys, 'BJ3jrUzddfuSrZHXSCxMUUQsjKEyLmuuyZebkcaFp2fg') -- Serum DEX V1
        or array_contains(account_keys, '4ckmDgGdxQoPDLUkDT3vHgSAkzA3QRdNq5ywwY4sUSJn') -- Serum DEX V1
        or array_contains(account_keys, '22Y43yTVxuUkoRKdm9thyRhQ3SdgQS7c7kB6UNCiaczD') -- Serum Swap
   
        or array_contains(account_keys, 'AMM55ShdkoGRB5jVYPjWziwk8m5MpwyDgsMWHaMSQWH6') -- Aldrin AMM
        or array_contains(account_keys, 'CURVGoZn8zycx6FXwwevgBTB2gVvdbGTEpvMJDbgs2t4') -- Aldrin AMM V2
       

        or array_contains(account_keys, 'RVKd61ztZW9GUwhRbbLoYVRE5Xf1B2tVscKqwZqXgEr') -- Raydium Liquidity Pool V2
        or array_contains(account_keys, '27haf8L6oxUeXrHrgEgsexjSY5hbVUWEmvv9Nyxg8vQv') -- Raydium Liquidity Pool V3
        or array_contains(account_keys, '675kPX9MHTjS2zt1qfr1NYHuzeLXfQM9H24wFSUt1Mp8') -- Raydium Liquidity Pool V4
        or array_contains(account_keys, 'EhhTKczWMGQt46ynNeRX1WfeagwwJd7ufHvCDjRxjo5Q') -- Raydium Stake
        or array_contains(account_keys, 'CBuCnLe26faBpcBP2fktp4rp8abpcAnTWft6ZrP5Q4T') -- Raydium Stake V4
        or array_contains(account_keys, '9KEPoZmtHUrBbhWN1v1KWLMkkvwY6WLtAVUCPRtRjP4z') -- Raydium Stake V5

        or array_contains(account_keys, 'SSwpkEEcbUqx4vtoEByFjSkhKdCT862DNVb52nZg1UZ') -- Saber Stable Swap
        or array_contains(account_keys, 'MERLuDFBMmsHnsBPZw2sDQZHvXFMwp8EdjudcU2HKky') -- Mercurial Stable Swap
       
        or array_contains(account_keys, '82yxjeMsvaURa4MbZZ7WZZHfobirZYkH1zF8fmeGtyaQ') -- Orca Aquafarm
        or array_contains(account_keys, 'DjVE6JNiYqPL2QXyCUUh8rNjHrbz9hXHNYt99MQ59qw1') -- Orca Token Swap
        or array_contains(account_keys, '9W959DqEETiGZocYWCQPaJ6sBmUzgfxXfqGeTEdp3aQP') -- Orca Token Swap V2
       
        or array_contains(account_keys, 'SSwpMgqNDsyV7mAgN9ady4bDVu5ySjmmXejXvy2vLt1') -- Step Finance Swap Program
       
        or array_contains(account_keys, 'SwaPpA9LAaLfeLi3a68M4DjnLqgtticKg6CnyNwgAC8') -- Swap Program ??


        )
        and block_time >= date_trunc('month', now() - interval '6 months')
        and block_time < date_trunc('month', now())
group by 1


