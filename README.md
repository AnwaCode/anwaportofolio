---
title: crud sederhana
publishDate: 2023-02-18 21:00:00
img: /assets/stock-2.jpg
img_alt: UKK
description: |
  Jalan UKK
tags:
-- avahvav
---

Untuk membuat sebuah CRUD sederhana menggunakan Laravel, Anda dapat mengikuti langkah-langkah berikut:

1. Persiapkan database Anda dan buat tabel kelas dengan kolom id_kelas (sebagai primary key), nama_kelas, kompetensi_keahlian.
2. Buat model Kelas dengan menggunakan perintah artisan php artisan make:model Kelas. Model ini akan merepresentasikan tabel kelas pada database.
3. Buat migration untuk tabel kelas dengan menggunakan perintah artisan php artisan make:migration create_kelas_table --create=kelas. Kemudian, buka file migration dan atur struktur tabel sesuai dengan kebutuhan Anda:



Schema::create('kelas', function (Blueprint $table) {
    $table->increments('id_kelas');
    $table->string('nama_kelas');
    $table->string('kompetensi_keahlian');
    $table->timestamps();
});




4. Jalankan migration dengan perintah php artisan migrate. 
5. Buat controller KelasController dengan menggunakan perintah php artisan make:controller KelasController --resource. Controller ini akan berisi fungsi-fungsi untuk melakukan operasi CRUD pada tabel kelas.




class KelasController extends Controller
{
    public function index()
    {
        // tampilkan semua data kelas
        $kelas = Kelas::all();
        return view('kelas.index', compact('kelas'));
    }

    public function create()
    {
        // tampilkan halaman form untuk menambahkan data kelas
        return view('kelas.create');
    }

    public function store(Request $request)
    {
        // validasi data yang diinputkan oleh user
        $validatedData = $request->validate([
            'nama_kelas' => 'required',
            'kompetensi_keahlian' => 'required',
        ]);

        // simpan data kelas ke database
        Kelas::create($validatedData);

        // redirect ke halaman index kelas dengan pesan sukses
        return redirect('/kelas')->with('success', 'Kelas berhasil ditambahkan');
    }

    public function show($id)
    {
        // tampilkan detail data kelas dengan id tertentu
        $kelas = Kelas::findOrFail($id);
        return view('kelas.show', compact('kelas'));
    }

    public function edit($id)
    {
        // tampilkan halaman form untuk mengedit data kelas dengan id tertentu
        $kelas = Kelas::findOrFail($id);
        return view('kelas.edit', compact('kelas'));
    }

    public function update(Request $request, $id)
    {
        // validasi data yang diinputkan oleh user
        $validatedData = $request->validate([
            'nama_kelas' => 'required',
            'kompetensi_keahlian' => 'required',
        ]);

        // update data kelas dengan id tertentu
        Kelas::findOrFail($id)->update($validatedData);

        // redirect ke halaman index kelas dengan pesan sukses
        return redirect('/kelas')->with('success', 'Kelas berhasil diupdate');
    }

    public function destroy($id)
    {
        // hapus data kelas dengan id tertentu
        Kelas::findOrFail($id)->delete();

        // redirect ke halaman index kelas dengan pesan sukses
        return redirect('/kelas')->with('success', 'Kelas berhasil dihapus');
    }
}





6. Buat route untuk KelasController pada file routes/web.php. Contoh:



Route::resource('kelas', 'KelasController');

Dengan menambahkan kode di atas, Laravel akan membuatkan secara otomatis semua route yang dibutuhkan untuk melakukan operasi CRUD pada tabel kelas.


7. Buat tampilan menggunakan view blade. Anda dapat membuat view untuk menampilkan daftar kelas (index.blade.php), form tambah kelas (create.blade.php), form edit kelas (edit.blade.php), dan detail kelas (show.blade.php). Contoh:



'index.blade.php'



@extends('layouts.app')

@section('content')
    <h1>Daftar Kelas</h1>

    @if(session('success'))
        <div class="alert alert-success">{{ session('success') }}</div>
    @endif

    <a href="{{ route('kelas.create') }}" class="btn btn-primary mb-3">Tambah Kelas</a>

    <table class="table table-bordered">
        <thead>
            <tr>
                <th>ID Kelas</th>
                <th>Nama Kelas</th>
                <th>Kompetensi Keahlian</th>
                <th>Aksi</th>
            </tr>
        </thead>
        <tbody>
            @foreach($kelas as $kls)
                <tr>
                    <td>{{ $kls->id_kelas }}</td>
                    <td>{{ $kls->nama_kelas }}</td>
                    <td>{{ $kls->kompetensi_keahlian }}</td>
                    <td>
                        <a href="{{ route('kelas.show', $kls->id_kelas) }}" class="btn btn-info btn-sm">Detail</a>
                        <a href="{{ route('kelas.edit', $kls->id_kelas) }}" class="btn btn-warning btn-sm">Edit</a>
                        <form action="{{ route('kelas.destroy', $kls->id_kelas) }}" method="post" class="d-inline">
                            @csrf
                            @method('DELETE')
                            <button type="submit" class="btn btn-danger btn-sm" onclick="return confirm('Apakah Anda yakin ingin menghapus kelas ini?')">Hapus</button>
                        </form>
                    </td>
                </tr>
            @endforeach
        </tbody>
    </table>
@endsection



create.blade.php


@extends('layouts.app')

@section('content')
    <h1>Tambah Kelas</h1>

    <form action="{{ route('kelas.store') }}" method="post">
        @csrf

        <div class="form-group">
            <label for="nama_kelas">Nama Kelas</label>
            <input type="text" name="nama_kelas" id="nama_kelas" class="form-control @error('nama_kelas') is-invalid @enderror" value="{{ old('nama_kelas') }}" autofocus required>
            @error('nama_kelas')
                <div class="invalid-feedback">{{ $message }}</div>
            @enderror
        </div>

        <div class="form-group">
            <label for="kompetensi_keahlian">Kompetensi Keahlian</label>
            <input type="text" name="kompetensi_keahlian" id="kompetensi_keahlian" class="form-control @error('kompetensi_keahlian') is-invalid @enderror" value="{{ old('kompetensi_keahlian') }}" required>
            @error('kompetensi_keahlian')
                <div class="invalid-feedback">{{ $message }}</div>
            @enderror
        </div>

        <button type="submit" class="btn btn-primary">Simpan</button>
    </form>
@endsection




'edit.blade.php'


@extends('layouts.app')

@section('content')
    <h1>Edit Kelas</h1>

    <form action="{{ route('kelas.update', $kelas->id_kelas) }}" method="post">
        @csrf
        @method('PUT')

        <div class="form-group">
            <label for="nama_kelas">Nama Kelas</label>
            <input type="text" name="nama_kelas" id="nama_kelas" class="form-control @error('nama_kelas') is-invalid @enderror" value="{{ old('nama_kelas', $kelas->nama_kelas) }}" autofocus required>
            @error('nama_kelas')
                <div class="invalid-feedback">{{ $message }}</div>
            @enderror
        </div>

        <div class="form-group">
            <label for="kompetensi_keahlian">Kompetensi Keahlian</label>
            <input type="text" name="kompetensi_keahlian" id="kompetensi_keahlian" class="form-control @error('kompetensi_keahlian') is-invalid @enderror" value="{{ old('kompetensi_keahlian', $kelas->kompetensi_keahlian) }}" required>
            @error('kompetensi_keahlian')
                <div class="invalid-feedback">{{ $message }}</div>
            @enderror
        </div>

        <button type="submit" class="btn btn-primary">Simpan</button>
    </form>
@endsection




'show.blade.php'



@extends('layouts.app')

@section('content')
<div class="container">
    <h1>Detail Kelas</h1>
    <div class="card">
        <div class="card-header">
            {{ $kelas->nama_kelas }}
        </div>
        <div class="card-body">
            <p><strong>ID Kelas:</strong> {{ $kelas->id_kelas }}</p>
            <p><strong>Kompetensi Keahlian:</strong> {{ $kelas->kompetensi_keahlian }}</p>
        </div>
        <div class="card-footer">
            <a href="{{ route('kelas.edit', $kelas) }}" class="btn btn-primary">Edit</a>
            <form action="{{ route('kelas.destroy', $kelas) }}" method="POST" class="d-inline">
                @csrf
                @method('DELETE')
                <button type="submit" class="btn btn-danger" onclick="return confirm('Apakah Anda yakin ingin menghapus kelas ini?')">Hapus</button>
            </form>
        </div>
    </div>
</div>
@endsection
