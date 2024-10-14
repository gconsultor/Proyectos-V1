# Proyectos-V1
'use client'

import { useState, useEffect } from 'react'
import { Tabs, TabsContent, TabsList, TabsTrigger } from "@/components/ui/tabs"
import { Button } from "@/components/ui/button"
import { Input } from "@/components/ui/input"
import { Dialog, DialogContent, DialogHeader, DialogTitle, DialogTrigger } from "@/components/ui/dialog"
import { CostosInterface } from './CostosInterface'
import { ProyeccionInterface } from './ProyeccionInterface'
import { ImpuestosInterface } from './ImpuestosInterface'
import { FlujoFondosInterface } from './FlujoFondosInterface'
import { AnalisisSensibilidadInterface } from './AnalisisSensibilidadInterface'
import { IngresosInterface } from './IngresosInterface'

export default function App() {
  const [costos, setCostos] = useState({
    fijos: [],
    variables: [],
    administracion: [],
    produccion: [],
    comerciales: [],
    manoDeObra: []
  })
  const [proyeccion, setProyeccion] = useState({
    ventas: [],
    egresos: []
  })
  const [impuestos, setImpuestos] = useState([])
  const [ingresos, setIngresos] = useState([])
  const [periodos, setPeriodos] = useState(5)
  const [nombreProyecto, setNombreProyecto] = useState('')
  const [proyectos, setProyectos] = useState([])

  useEffect(() => {
    const proyectosGuardados = localStorage.getItem('proyectos')
    if (proyectosGuardados) {
      setProyectos(JSON.parse(proyectosGuardados))
    }
  }, [])

  const guardarProyecto = () => {
    if (!nombreProyecto.trim()) {
      alert('Por favor, ingrese un nombre para el proyecto')
      return
    }
    const nuevoProyecto = {
      nombre: nombreProyecto,
      costos,
      proyeccion,
      impuestos,
      ingresos,
      periodos
    }
    const nuevosProyectos = [...proyectos, nuevoProyecto]
    setProyectos(nuevosProyectos)
    localStorage.setItem('proyectos', JSON.stringify(nuevosProyectos))
    setNombreProyecto('')
  }

  const cargarProyecto = (proyecto) => {
    setCostos(proyecto.costos)
    setProyeccion(proyecto.proyeccion)
    setImpuestos(proyecto.impuestos)
    setIngresos(proyecto.ingresos || [])
    setPeriodos(proyecto.periodos)
  }

  return (
    <div className="container mx-auto p-4">
      <h1 className="text-2xl font-bold mb-4">Evaluación de Proyectos de Inversión</h1>
      <div className="mb-4 flex space-x-2">
        <Input
          placeholder="Nombre del proyecto"
          value={nombreProyecto}
          onChange={(e) => setNombreProyecto(e.target.value)}
        />
        <Button onClick={guardarProyecto}>Guardar Proyecto</Button>
        <Dialog>
          <DialogTrigger asChild>
            <Button variant="outline">Cargar Proyecto</Button>
          </DialogTrigger>
          <DialogContent>
            <DialogHeader>
              <DialogTitle>Seleccionar Proyecto</DialogTitle>
            </DialogHeader>
            <div className="grid gap-4">
              {proyectos.map((proyecto, index) => (
                <Button key={index} onClick={() => cargarProyecto(proyecto)}>
                  {proyecto.nombre}
                </Button>
              ))}
            </div>
          </DialogContent>
        </Dialog>
      </div>
      <Tabs defaultValue="ingresos">
        <TabsList>
          <TabsTrigger value="ingresos">Ingresos</TabsTrigger>
          <TabsTrigger value="costos">Costos</TabsTrigger>
          <TabsTrigger value="proyeccion">Proyección</TabsTrigger>
          <TabsTrigger value="impuestos">Impuestos</TabsTrigger>
          <TabsTrigger value="flujofondos">Flujo de Fondos</TabsTrigger>
          <TabsTrigger value="analisis">Análisis de Sensibilidad</TabsTrigger>
        </TabsList>
        <TabsContent value="ingresos">
          <IngresosInterface ingresos={ingresos} setIngresos={setIngresos} />
        </TabsContent>
        <TabsContent value="costos">
          <CostosInterface costos={costos} setCostos={setCostos} />
        </TabsContent>
        <TabsContent value="proyeccion">
          <ProyeccionInterface 
            proyeccion={proyeccion} 
            setProyeccion={setProyeccion} 
            costos={costos}
            periodos={periodos}
            setPeriodos={setPeriodos}
          />
        </TabsContent>
        <TabsContent value="impuestos">
          <ImpuestosInterface impuestos={impuestos} setImpuestos={setImpuestos} />
        </TabsContent>
        <TabsContent value="flujofondos">
          <FlujoFondosInterface 
            costos={costos}
            proyeccion={proyeccion}
            impuestos={impuestos}
            ingresos={ingresos}
            periodos={periodos}
          />
        </TabsContent>
        <TabsContent value="analisis">
          <AnalisisSensibilidadInterface 
            costos={costos}
            proyeccion={proyeccion}
            impuestos={impuestos}
            ingresos={ingresos}
            periodos={periodos}
          />
        </TabsContent>
      </Tabs>
    </div>
  )
}
