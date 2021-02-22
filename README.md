# TodoList

[![Image from Gyazo](https://i.gyazo.com/46ee1f52451cef30bdfea27cd1c55fe8.png)](https://gyazo.com/46ee1f52451cef30bdfea27cd1c55fe8)

#### 参考(youtube)
[Swift To Do List App for Beginners (Make First App, Xcode 12, 2021, iOS) - Swift 5](https://www.youtube.com/watch?v=Vqo36o9fSMM

## ViewController 全文
```swift
import UIKit

class ViewController: UIViewController, UITableViewDataSource {
    
    private let table: UITableView = {
        let table = UITableView()
        table.register(UITableViewCell.self, forCellReuseIdentifier: "cell")
        
        return table
    }()

    
    var items = [String]()
    
    
    override func viewDidLoad() {
        super.viewDidLoad()
        // UserDefaultsに一時保存したitemsを呼び出し
        items = UserDefaults.standard.stringArray(forKey: "items") ?? [] // ??[] is default value
        
        // ナビゲーションタイトル
        title = "To Do List"
        
        //テーブルビューの設置
        view.addSubview(table)
        table.frame = view.bounds
        table.dataSource = self // dataSourceメソッドはこのファイルに記述
        
        // ナビゲーションの右側に+ボタンを設置、actionにメソッドを指定
        navigationItem.rightBarButtonItem = UIBarButtonItem(barButtonSystemItem: .add,
                                                            target: self,
                                                            action: #selector(didTapAdd))
    }
    
    @objc private func didTapAdd() {
        let alert = UIAlertController(title: "New Item", message: "Enter new to do list item!", preferredStyle: .alert)
        
        alert.addTextField { field in
            field.placeholder = "Enter item..."
        }
        alert.addAction(UIAlertAction(title: "Cancel", style: .cancel, handler: nil))
        // handlerの処理をクロージャで設置。(_)は"action: UIAlertAction型"が省略されている
        alert.addAction(UIAlertAction(title: "Done", style: .default, handler: { [weak self] (_) in
            if let field = alert.textFields?.first {
                if let text = field.text, !text.isEmpty {
                    // Enter new to do list item
                    print(text)
                    DispatchQueue.main.async {
                        // 保存するための設定
                        // UserDefaults配列に要素がある場合はそれをcurrentItemsに代入、何もなければデフォルトの[]を代入
                        var currentItems = UserDefaults.standard.stringArray(forKey: "items") ?? []
                        // 配列に要素を追加
                        currentItems.append(text)
                        // 更新された配列をUserDefaultsに保存
                        UserDefaults.standard.setValue(currentItems, forKey: "items")
                        // itemsに追加(tableviewcellに表示)
                        self?.items.append(text)
                        self?.table.reloadData()
                    }
                }
            }
        } ))
        
        present(alert, animated: true, completion: nil)
    }
    
    
    override func viewDidLayoutSubviews() {
        super.viewDidLayoutSubviews()
        //table.frame = view.bounds // 見本でなぜsubviewsに記述するのかよくわかってない。のでviewdidloadに移動した
    }


    // dataSourceメソッド
    func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        
        return items.count
    }
    func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        let cell = table.dequeueReusableCell(withIdentifier: "cell", for: indexPath)
        
        cell.textLabel?.text = items[indexPath.row]
        return cell
    }
        
}

```
