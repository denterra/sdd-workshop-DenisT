# Архітектура навчального сайту Фундації Лятошинського

Діаграма показує, звідки сторінки в `app/[lang]/` отримують вміст: таблиці творів і наукових праць викликають HTTP API, а серверні компоненти можуть звертатися до сервісів напряму. Контейнер `app/di/container.ts` зв’язує сервіси з `app/services/` та репозиторії з `app/infrastructure/repositories/` через реєстрації в `composedServices.module.ts` і `repositories.module.ts`. Репозиторії читають демобазу MongoDB через моделі в `app/infrastructure/models/`, тоді як опис архівного фонду й таблиця його документів беруться з локальних файлів.

```mermaid
flowchart TD
    subgraph catalogs["Творчість і дослідження"]
        music["artistry/page.tsx"] --> mt["MusicTableSection.tsx"]
        mt -->|GET| ma["/api/compositions/data<br/>/api/compositions/filters<br/>/api/compositions/titles"]
        ma --> ms["artistryService.ts"] --> mr["compositions.repository.ts"] --> mm["models/artistry/"]
        mt -->|Відтворити аудіо| blob["GET /api/blob-url"]
        blob --> files["public/"]

        research["research/page.tsx"] --> wt["WorkTableSection.tsx"]
        wt -->|GET| wa["/api/scientific-works/data<br/>/api/scientific-works/filters<br/>/api/scientific-works/titles"]
        wa --> ws["scientificWorks.ts"] --> wr["scientificWorks.repository.ts"] --> wm["models/scientific-works/"]
    end

    subgraph archive["Архів"]
        list["archive/page.tsx"] -->|GET| fa["/api/funds"]
        fa --> fs["fundsService.ts"]
        casePage["archive/[fund]/[case]/page.tsx"] -->|За ідентифікатором справи| fs
        fs --> fr["funds.repository.ts"] --> fm["models/archive/<br/>Fund.ts · Case.ts · Document.ts"]
        fund["archive/[fund]/page.tsx"] -->|Демоопис і перелік документів| fixture["FundSummaryHeader.content.ts<br/>documents.mock.ts"]
    end

    subgraph editorial["Вміст сторінок і медіацентр"]
        pages["page.tsx · about-us/page.tsx<br/>biography/page.tsx · privacy-policy/page.tsx<br/>research/page.tsx (вступ)"]
        pages --> resolve["resolvePageData.ts"]
        resolve -->|Публікація або чернетка| ps["pagesDataService.ts"]
        coop["cooperation/page.tsx"] -->|Формати партнерства| ps
        ps --> pr["pagesData.repository.ts"] --> pm["models/pages/<br/>pages.ts · draftPages.model.ts"]

        news["news/page.tsx"] --> ns["newsService.ts"] --> nr["news.repository.ts"] --> nm["news.model.ts"]
        news --> mentions["mediaMentionService.ts"] --> mentionRepo["mediaMention.repository.ts"] --> mentionModel["mediaMention.model.ts"]
        news -->|Вкладка подій| events["MediaCenter.tsx"] --> eventData["EventItem.fixture.ts"]
    end

    subgraph shared["Навігація, контакти та інші сторінки"]
        layout["layout.tsx"] --> header["Header.server.tsx"]
        layout --> footer["Footer.tsx"]
        header --> hs["header/headerService.ts"]
        header --> footSvc["footerService.ts"]
        footer --> footSvc
        contactPages["contacts/page.tsx · support-us/page.tsx<br/>cooperation/page.tsx (OfferCollaboration.tsx)"] -->|Контактні дані| footSvc
        hs --> info["foundationInfo.repository.ts"]
        hs --> nav["navigation.repository.ts"]
        footSvc --> info
        footSvc --> nav
        info --> infoModel["models/foundation-info/"]
        nav --> navModel["models/navigation/"]
        formPages["contacts/page.tsx · cooperation/page.tsx"] --> form["OfferCollaborationForm.tsx"]
        form -->|POST| collab["/api/collaboration<br/>Перевіряє дані й повертає демовідповідь"]
        terms["terms/page.tsx"] --> termsUI["TermsOfUse.tsx"]
        war["war-in-ukraine/page.tsx"] --> warData["war.const.ts"]
    end

    %% Невидимі зв'язки задають лише розміщення розділів.
    catalogs ~~~ archive
    archive ~~~ editorial
    editorial ~~~ shared
```
