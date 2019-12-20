
# RAC+MVVM
> 什么是mvvm?，简单的一个列表，然后请求数据展示。我们看看ViewController。
``` js
    import UIKit
    import RxSwift
    import RxCocoa
    class LanguageListViewController: UIViewController {

      let disposeBag = DisposeBag()
      var viewModel: LanguageListViewModel!

      @IBOutlet private weak var tableView: UITableView!
      private let cancelButton = UIBarButtonItem(barButtonSystemItem: .cancel, target: nil, action: nil)

      override func viewDidLoad() {
          super.viewDidLoad()

          setupUI()
          setupBindings()
      }

      private func setupUI() {
          navigationItem.leftBarButtonItem = cancelButton
          navigationItem.title = "Choose a language"

          tableView.rowHeight = 48.0
      }

      private func setupBindings() {
          viewModel.languages
              .observeOn(MainScheduler.instance)
              .bind(to: tableView.rx.items(cellIdentifier: "LanguageCell", cellType: UITableViewCell.self)) { (_, language, cell) in
                  cell.textLabel?.text = language
                  cell.selectionStyle = .none
              }
              .disposed(by: disposeBag)

          tableView.rx.modelSelected(String.self)
              .bind(to: viewModel.selectLanguage)
              .disposed(by: disposeBag)

          cancelButton.rx.tap
              .bind(to: viewModel.cancel)
              .disposed(by: disposeBag)
      }
    }
```
> 让我们看看ViewModel
``` js
import RxSwift

class LanguageListViewModel {

    // MARK: - Inputs
    let selectLanguage: AnyObserver<String>
    let cancel: AnyObserver<Void>

    // MARK: - Outputs
    let languages: Observable<[String]>
    let didSelectLanguage: Observable<String>
    let didCancel: Observable<Void>

    init(githubService: GithubService = GithubService()) {
        self.languages = githubService.getLanguageList()

        let _selectLanguage = PublishSubject<String>()
        self.selectLanguage = _selectLanguage.asObserver()
        self.didSelectLanguage = _selectLanguage.asObservable()

        let _cancel = PublishSubject<Void>()
        self.cancel = _cancel.asObserver()
        self.didCancel = _cancel.asObservable()
    }
}
```

> model无需多说

```js
import Foundation

struct Repository {
    let fullName: String
    let description: String
    let starsCount: Int
    let url: String
}

extension Repository {
    init?(from json: [String: Any]) {
        guard
            let fullName = json["full_name"] as? String,
            let description = json["description"] as? String,
            let starsCount = json["stargazers_count"] as? Int,
            let url = json["html_url"] as? String
        else { return nil }

        self.init(fullName: fullName, description: description, starsCount: starsCount, url: url)
    }
}

extension Repository: Equatable {
    static func == (lhs: Repository, rhs: Repository) -> Bool {
        return lhs.fullName == rhs.fullName
            && lhs.description == rhs.description
            && lhs.starsCount == rhs.starsCount
            && lhs.url == rhs.url
    }
}
```