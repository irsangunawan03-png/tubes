package main

import (
	"fmt"
)

// Struct untuk merepresentasikan akun ATM
type Account struct {
	PIN    string
	Balance float64
}

// Simulasi database akun
var accounts = map[string]*Account{
	"103062530006": {PIN: "1234", Balance: 1000000.0},
	"103062500164": {PIN: "1234", Balance: 1000000.0},
	"103062500065": {PIN: "1234", Balance: 1000000.0},
	"103062500060": {PIN: "1234", Balance: 1000000.0},
}

func main() {
	fmt.Println("Selamat datang di ATM Simulator")
	fmt.Print("Masukkan nomor kartu: ")
	var cardNumber string
	fmt.Scanln(&cardNumber)

	account, exists := accounts[cardNumber]
	if !exists {
		fmt.Println("Kartu tidak ditemukan.")
		return
	}

	fmt.Print("Masukkan PIN: ")
	var pin string
	fmt.Scanln(&pin)

	if pin != account.PIN {
		fmt.Println("PIN salah.")
		return
	}

	fmt.Println("Login berhasil!")

	for {
		fmt.Println("\nPilih menu:")
		fmt.Println("1. Cek Saldo")
		fmt.Println("2. Tarik Tunai")
		fmt.Println("3. Deposit")
		fmt.Println("4. Transfer")
		fmt.Println("5. Keluar")
		fmt.Print("Pilihan: ")

		var choice int
		fmt.Scanln(&choice)

		switch choice {
		case 1:
			fmt.Printf("Saldo Anda: Rp %.2f\n", account.Balance)
		case 2:
			fmt.Print("Masukkan jumlah tarik tunai: Rp ")
			var amount float64
			fmt.Scanln(&amount)
			if amount > account.Balance {
				fmt.Println("Saldo tidak cukup.")
			} else {
				account.Balance -= amount
				fmt.Printf("Tarik tunai berhasil. Saldo sekarang: Rp %.2f\n", account.Balance)
			}
		case 3:
			fmt.Print("Masukkan jumlah deposit: Rp ")
			var amount float64
			fmt.Scanln(&amount)
			account.Balance += amount
			fmt.Printf("Deposit berhasil. Saldo sekarang: Rp %.2f\n", account.Balance)
		case 4:
			fmt.Print("Masukkan nomor rekening tujuan: ")
			var targetCard string
			fmt.Scanln(&targetCard)
			targetAccount, exists := accounts[targetCard]
			if !exists {
				fmt.Printf("Rekening tujuan %s tidak ditemukan. Apakah Anda ingin menambahkannya? (y/n): ", targetCard)
				var addAccount string
				fmt.Scanln(&addAccount)
				if addAccount == "y" {
					fmt.Print("Masukkan PIN untuk rekening baru: ")
					var newPIN string
					fmt.Scanln(&newPIN)
					fmt.Print("Masukkan saldo awal: Rp ")
					var newBalance float64
					fmt.Scanln(&newBalance)
					accounts[targetCard] = &Account{PIN: newPIN, Balance: newBalance}
					fmt.Println("Rekening tujuan berhasil ditambahkan.")
					targetAccount = accounts[targetCard] // Setelah ditambah, lanjut transfer
				} else {
					continue // Kembali ke menu utama
				}
			}
			if targetCard == cardNumber {
				fmt.Println("Tidak bisa transfer ke rekening sendiri.")
				continue
			}
			fmt.Print("Masukkan jumlah transfer: Rp ")
			var amount float64
			fmt.Scanln(&amount)
			if amount > account.Balance {
				fmt.Println("Saldo tidak cukup.")
			} else {
				account.Balance -= amount
				targetAccount.Balance += amount
				fmt.Printf("Transfer berhasil. Saldo sekarang: Rp %.2f\n", account.Balance)
			}
		case 5:
			fmt.Println("Terima kasih telah menggunakan ATM.")
			return
		default:
			fmt.Println("Pilihan tidak valid.")
		}
	}
}
